<!-- components/CreateWalletTab.vue -->
<template>
	<section id="steps">
		<div class="block steps">
			<div class="container">
				<div class="">
					<div class="text-4xl mt-10 mb-10">Invite a friend</div>

					<div class="text-lg">
						By clicking the ”Invite Friend” button, you perform a private
						transfer of 30% of your available PAS from your Incognitee wallet to
						another wallet with an invite link. You can share this with your
						friends and let them participate.
					</div>

					<div class="mt-10 mb-8">
						<template
							v-if="accountStore.incogniteeBalance > min_incognitee_balance"
						>
							<UButton class="btn btn_gradient" @click="inviteFriend"
								>Invite Friend</UButton
							>
							<div>{{ topStatus }}</div>
						</template>
						<template
							v-if="accountStore.incogniteeBalance <= min_incognitee_balance"
						>
							<div class="border-l-4 border-yellow-400 bg-yellow-50 p-4">
								<div class="flex">
									<div class="flex-shrink-0">
										<ExclamationTriangleIcon
											class="h-5 w-5 text-yellow-400"
											aria-hidden="true"
										/>
									</div>
									<div class="ml-3">
										<p class="text-sm text-yellow-700">
											You don't have enough balance on Incognitee to invite
											someone. Please go back to step 2.
											{{ ' ' }}
										</p>
									</div>
								</div>
							</div>
						</template>
					</div>
					<div class="mt-10 mb-3">
						<input
							type="text"
							ref="urlToShareWithFriend"
							class="dynamic-width border-2 border-gray-200 p-2"
							style="color: black"
							:value="inviteUrl"
							readonly
						/>
					</div>
					<div class="mt-3 mb-8">
						<button @click="copyToClipboard" class="button">Copy</button>
					</div>
				</div>
			</div>
		</div>
	</section>
</template>

<script setup lang="ts">
import { ExclamationTriangleIcon } from '@heroicons/vue/20/solid'
import { Keyring } from '@polkadot/keyring'
import { formatBalance, u8aToHex } from '@polkadot/util'
import { mnemonicGenerate, mnemonicToMiniSecret } from '@polkadot/util-crypto'
import { ref } from 'vue'

import { useAccount } from '@/store/account.ts'
import { useIncognitee } from '@/store/incognitee.ts'

const accountStore = useAccount()
const incogniteeStore = useIncognitee()
const topStatus = ref('')

const inviteUrl = ref('click "invite friend" to generate a new wallet for them')

const min_incognitee_balance = 0.02 * 10 ** 10

formatBalance.setDefaults({
	decimals: 10,
	unit: 'PAS',
})

const copyToClipboard = () => {
	navigator.clipboard
		.writeText(inviteUrl.value)
		.then(() =>
			alert('copied invite url to clipboard. please share it with your friend')
		)
}
const inviteFriend = () => {
	console.log('creating wallet for your friend')
	topStatus.value =
		'⌛ sending 30% of your funds to a fresh wallet for your friend. you should see your incognitee balance decrease. make sure to copy the url below and share it with your friend'
	const generatedMnemonic = mnemonicGenerate()
	const localKeyring = new Keyring({ type: 'sr25519', ss58Format: 42 })
	const newAccount = localKeyring.addFromMnemonic(generatedMnemonic, {
		name: 'fresh',
	})
	const seed = mnemonicToMiniSecret(generatedMnemonic)
	const privateKeyHex = u8aToHex(seed)
	console.log(
		`friend account ${newAccount.address} with private key in hex: ${privateKeyHex}`
	)
	inviteUrl.value =
		window.location.protocol +
		'//' +
		window.location.hostname +
		(window.location.port ? `:${window.location.port}` : '') +
		'/?seed=' +
		privateKeyHex

	console.log("sending 30% of your funds to your friend's account")
	let balance = accountStore.incogniteeBalance
	// todo! instead of sending 30% we should check fees explicitly and handle edge cases
	let amount = Math.floor(0.3 * balance)
	let signer = accountStore.account
	console.log(
		`sending ${formatBalance(amount)} from ${signer.address} privately to ${
			newAccount.address
		}`
	)
	incogniteeStore.api
		.trustedBalanceTransfer(
			signer,
			incogniteeStore.shard,
			incogniteeStore.fingerprint,
			signer.address,
			newAccount.address,
			amount
		)
		.then(hash => {
			console.log(`trustedOperationHash: ${hash}`)
			topStatus.value =
				"😀 Success: sent 30% of your funds to a fresh wallet for your friend. you should see your incognitee balance decrease. Please copy the url below and share it with your friend. It's all they need"
		})
}
</script>

<style>
.button {
	display: flex;
	padding: 14px 24px;
	align-items: flex-start;
	gap: 10px;
	border-radius: 12px;
	border: 2px solid var(--Transparent-White-20, rgba(255, 255, 255, 0.2));
	backdrop-filter: blur(30px);
}

.dynamic-width::after {
	width: 500px;
	border-radius: 8px;
	border-color: #24ad7c;
	height: 55px;
}

.dynamic-width {
	width: 500px;
	border-radius: 8px;
	border-color: #24ad7c;
	height: 55px;
}
</style>
