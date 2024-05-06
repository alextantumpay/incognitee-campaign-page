<template>
	<section id="partner">
		<div class="block partner">
			<div class="container">
				<div class="column column_medium partner__column">
					<h2 class="title title_h2 partner__title">Rules</h2>

					<ul
						class="paragraph paragraph_medium margin_large partner__description list-disc list-outside"
					>
						<li>Every shielding event will give you 1 Point</li>
						<li>Every unshield event will give you 2 Points</li>
						<li>
							Perform step 5 to be considered as a participant and get
							additional 5 points
						</li>
						<li>
							Don’t post multiple times with the same X account. However,
							sending us a message over simpleX and posting on nostr will give
							you additional 5 points each
						</li>
						<li>
							You can perform steps 2-4 repeatedly until you run out of funds
						</li>
						<li>
							This campaign aims to test the limits of incognitee by generating
							substantial traffic. Should the chain reach its limit and maybe
							even become inoperable at any point, the campaign will conclude.
							Winners will then be determined based on the data available up to
							that moment.
						</li>
						<li>The top 3 participants will win a prize.</li>
					</ul>

					<h2 class="title title_h2 partner__title">Info</h2>
					<li>
						Your invites to other people are invisible even to us, but you'll
						get good karma for sure!
					</li>
					<li>
						If you open up an invite friend link, make sure to check wether your
						incognitee balance shows an available amount
					</li>
					<li>
						Once exhausted, you can refund your Paseo balance every 24h using
						the faucet in step 2
					</li>
					<li>
						Don't close your browser tab without saving the link. Otherwise you
						may lose access to your testing wallet. (This is a static webpage
						and we store no cookies to protect your privacy)
					</li>
					<li>The event starts on the 7th May and ends 21st May</li>
					<li>
						Winners will be announced latest 2 weeks after the event via X.
					</li>
					<li>
						If you encounter issues, please send a mail with a description to
						<a href="mailto:hello@integritee.network"
							>hello@integritee.network</a
						>
						or contact us via Discord
					</li>
				</div>

				<div class="partner__image-wrapper">
					<!-- <img src="/img/index/partner/polygons.svg" class="partner__image" alt="Polygons" /> -->
					<img
						src="/img/index/rules/rules.svg"
						class="partner__image"
						alt="Polygons"
					/>
				</div>
			</div>
		</div>
	</section>
</template>
<script setup></script>
<style lang="scss">
.partner {
	.container {
		position: relative;
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 50px;

		@include md {
			gap: 15px;
		}

		@include sm {
			flex-direction: column-reverse;
		}
	}

	&__image {
		width: 100%;

		@include sm {
			width: 90%;
		}
	}

	&__image-wrapper {
		position: relative;

		@include sm {
			text-align: center;
			margin-bottom: 70px;
		}

		@include xsm {
			margin-bottom: 50px;
		}
	}

	&__image-gradient {
		left: 50%;
		top: 50%;
		transform: translateX(-50%) translateY(-50%);
		width: 950px;
		height: 445px;

		@include slg {
			height: 338px;
			width: 676px;
		}

		@include md {
			height: 309px;
			width: 618px;
		}

		@include sm {
			height: 330px;
			width: 660px;
		}

		@include xsm {
			height: 258px;
			width: 516px;
		}
	}

	&__column {
		flex-shrink: 0;

		@include lg {
			width: 560px;
		}

		@include slg {
			width: 480px;
		}

		@include slg {
			width: 410px;
		}

		@include sm {
			width: 100%;
		}
	}

	&__title {
		@include slg {
			br {
				display: none;
			}
		}
	}
}
</style>
