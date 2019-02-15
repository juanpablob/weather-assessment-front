<template>
	<div class="forecast" :class="[(size === 'small') ? 'forecast--small' : 'forcast--big']">
		<div class="row align-items-center" :class="[(size === 'small') ? 'no-gutters' : null]">
			<div class="col-6 text-right">
				<i :class="'forecast__icon icon icon-' + data.weather.icon"></i>
			</div>

			<div class="col-6" v-if="size === 'big'">
				<h3 class="forecast__temperature">{{ data.temperature.current }}º</h3>
				<p class="forecast__description">{{ data.weather.description }}</p>
			</div>

			<div class="col-6" v-if="size === 'small'">
				<h3 class="forecast__temperature">{{ data.temperature.max}}º</h3>
				<p class="forecast__description">{{ data.temperature.min }}º</p>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: 'Forecast',
	props: [
		'data',
		'size'
	],
	data() {
		return {
		}
	},
	methods: {
	}
}
</script>

<style lang="scss" scoped>
	.forecast {
		&__icon {
			font-size: 100px;
		}

		&__temperature {
			margin: 0;
			font-size: 60px;
			line-height: 1;
			font-weight: 300;
		}

		&__description {
			margin: 0;
			color: #c1c2c5;
			font-size: 20px;
			text-transform: capitalize;
		}

		// Small modifier
		&--small &__icon {
			display: inline-block;
			margin-right: 5px;
			font-size: 40px;
		}

		&--small &__temperature {
			margin-left: 5px;
			font-size: 18px;
		}

		&--small &__description {
			margin-left: 5px;
			font-size: 14px;
		}
	}
</style>
