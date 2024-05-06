<!-- components/CreateWalletTab.vue -->
<template>
	<section id="steps">
		<div class="block steps">
			<div class="container">
				<div class="grid grid-rows-3 grid-flow-col gap-4">
					<div class="text-4xl mt-10">Create a Wallet</div>
					<div class="text-lg">
						By clicking the “Create Wallet” button, you will create a new wallet
						on Paseo and Incognitee.
					</div>
					<div>
						<NuxtLink
							target="blank"
							class="btn btn_gradient"
							@click="createWallet"
							>Create a Wallet
						</NuxtLink>
					</div>
				</div>
			</div>
		</div>
	</section>
</template>

<script setup lang="ts">
import { useAccount } from '@/store/account.ts'
import { Keyring } from '@polkadot/keyring'
import { hexToU8a, u8aToHex } from '@polkadot/util'
import {
	cryptoWaitReady,
	mnemonicGenerate,
	mnemonicToMiniSecret,
} from '@polkadot/util-crypto'
import { defineComponent, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const accountStore = useAccount()

const emit = defineEmits(['change-tab'])
const createWallet = async () => {
	console.log('creating fresh wallet')
	await cryptoWaitReady()
	const generatedMnemonic = mnemonicGenerate()
	const localKeyring = new Keyring({ type: 'sr25519', ss58Format: 42 })
	const newAccount = localKeyring.addFromMnemonic(generatedMnemonic, {
		name: 'fresh',
	})
	const seed = mnemonicToMiniSecret(generatedMnemonic)
	const privateKeyHex = u8aToHex(seed)
	console.log(`Private Key in Hex: ${privateKeyHex}`)
	// change url to contain new seed to allow bookmarking
	await router.push({
		query: { seed: privateKeyHex },
	})
	accountStore.setAccount(newAccount)
	emit('change-tab', 1)
}

onMounted(() => {
	const seedHex = router.currentRoute.value.query.seed
	if (seedHex) {
		console.log('found seed in url: ' + seedHex)
		cryptoWaitReady().then(() => {
			const localKeyring = new Keyring({ type: 'sr25519' })
			const account = localKeyring.addFromSeed(hexToU8a(seedHex))
			accountStore.setAccount(account)
			emit('change-tab', 1)
		})
	}
})

defineComponent({
	emits: ['change-tab'],
})
</script>
