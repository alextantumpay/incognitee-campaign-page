<!-- components/CreateWalletTab.vue -->
<template>
	<section id="steps">
		<div class="block steps">
			<div class="container">
				<div class="grid gap-4">
					<div class="text-4xl mt-10">Shield PAS tokens to Incognitee</div>
					<div class="text-lg">
						<template
							v-if="
								accountStore.paseoBalance > existential_deposit * 10 ||
								(accountStore.paseoBalance > 0 && txStatus != '')
							"
						>
							Shielding your tokens means that you send them from Paseo to
							Incognitee where you can then transfer them privately

							<div class="m-10">
								<NuxtLink
									target="blank"
									class="m-5 btn btn_gradient"
									@click="shield"
									>Shield PAS to Incognitee
								</NuxtLink>

								>
								<div>{{ txStatus }}</div>
							</div>
						</template>

						<template v-else>
							You don’t have enough Paseo PAS on your account. Copy your address
							and follow the link below to obtain some PAS for free from the
							Paseo faucet.

							<div class="container mx-auto mt-10">
								<div class="flex space-x-4 justify-center mt-10">
									<a class="button btn" @click="copyToClipboard"
										>Copy My Address</a
									>
									<a
										href="https://faucet.polkadot.io/paseo"
										target="_blank"
										class="btn btn_gradient"
										>Get free PAS tokens</a
									>
								</div>
							</div>
						</template>
					</div>
				</div>
			</div>
		</div>
	</section>
</template>

<script setup lang="ts">
import { useAccount } from '@/store/account.ts'
import { useIncognitee } from '@/store/incognitee.ts'
import { ApiPromise, WsProvider } from '@polkadot/api'
import { ref } from 'vue'

const existential_deposit = 10000000000
const txStatus = ref('')
const accountStore = useAccount()
const incogniteeStore = useIncognitee()
const emit = defineEmits(['change-tab'])
const txResHandler = ({ events = [], status, txHash }) => {
	status.isFinalized
		? (txStatus.value = `😀 Finalized. You should see your Incognitee balance increase in seconds. Please proceed to the next tab and invite a friend`)
		: (txStatus.value = `⌛ Current transaction status: ${status.type}. please be patient a few more seconds. you should see your Paseo balance going down`)

	// Loop through Vec<EventRecord> to display all events
	events.forEach(({ _, event: { data, method, section } }) => {
		if (section + ':' + method === 'system:ExtrinsicFailed') {
			// extract the data for this event
			const [dispatchError, dispatchInfo] = data
			console.log(`dispatchinfo: ${dispatchInfo}`)
			let errorInfo

			// decode the error
			if (dispatchError.isModule) {
				// for module errors, we have the section indexed, lookup
				// (For specific known errors, we can also do a check against the
				// api.errors.<module>.<ErrorName>.is(dispatchError.asModule) guard)
				const mod = dispatchError.asModule
				const error = api.registry.findMetaError(
					new Uint8Array([
						mod.index.toNumber(),
						bnFromHex(mod.error.toHex().slice(0, 4)).toNumber(),
					])
				)
				let message = `${error.section}.${error.name}${
					Array.isArray(error.docs)
						? `(${error.docs.join('')})`
						: error.docs || ''
				}`

				errorInfo = `${message}`
				console.log(`Error-info::${JSON.stringify(error)}`)
			} else {
				// Other, CannotLookup, BadOrigin, no extra info
				errorInfo = dispatchError.toString()
			}
			txStatus.value = `😞 Transaction Failed! ${section}.${method}::${errorInfo}`
		} else if (section + ':' + method === 'system:ExtrinsicSuccess') {
			txStatus.value`❤️️ Transaction successful! please proceed to the next tab and invite a friend`
		}
	})
}

const txErrHandler = err =>
	(txStatus.value = `😞 Transaction Failed: ${err.toString()}`)
const shield = async () => {
	console.log('shielding 90% of all your PAS')
	txStatus.value = '⌛ connecting to Paseo....please be patient'
	if (incogniteeStore.vault) {
		let balance = accountStore.paseoBalance
		let amount = Math.floor(0.9 * balance)
		if (balance - amount < existential_deposit) {
			alert(
				'Your PAS balance would fall below the existential deposit. Please obtain more PAS from the faucet'
			)
			txStatus.value = ''
			return
		}
		console.log(`sending ${amount} to vault: ${incogniteeStore.vault}`)
		const wsProvider = new WsProvider('wss://rpc.ibp.network/paseo')
		const api = await ApiPromise.create({ provider: wsProvider })
		console.log('api initialized for shielding')
		await api.tx.balances
			.transferKeepAlive(incogniteeStore.vault, amount)
			.signAndSend(accountStore.account, txResHandler)
			.catch(txErrHandler)
	}
}

const copyToClipboard = () => {
	navigator.clipboard
		.writeText(accountStore.getAddress)
		.then(() =>
			alert(
				'copied your account address to clipboard. Please paste it into the address field on the Paseo faucet.'
			)
		)
}
</script>
