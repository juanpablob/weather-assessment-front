<template>
	<div class="container">
		<div class="row h-100">
			<div class="col-12 col-lg-7 m-auto">
				<!-- weather widget -->
				<div class="weather">
					<header class="weather__header">
						<div class="row justify-content-between align-items-center">
							<div class="col-2"></div>

							<div class="col-8">
								<form v-on:submit.prevent="changeLocation">
									<input type="search" class="form-control" v-model="location" :disabled="loading" placeholder="Enter City or Post Code">
									<i :disabled="loading" class="icon icon-search"></i>
								</form>
							</div>

							<div class="col-2 text-right">
								<button type="button" title="Refresh" @click="getForecast"><i class="icon icon-refresh"></i></button>
							</div>
						</div>
					</header>

					<content class="weather__content">
						<div v-if="!forecast.current" class="text-center">
							<div class="spinner-border text-center" role="status">
								<span class="sr-only">Loading...</span>
							</div>
						</div>

						<Forecast v-if="forecast.current" :data="forecast.current" :size="'big'" :disabled="loading" />
					</content>

					<section class="weather__forecast">
						<ul v-if="forecast.daily">
							<li class="weather__day" v-for="day in forecast.daily" :disabled="loading">
								<h4>{{ day.timestamp | moment('dddd') }}</h4>

								<Forecast :data="day" :size="'small'" />
							</li>
						</ul>
					</section>
				</div>
				<!-- /weather widget -->

				<section class="log">
					<p class="text-center"><button class="log__trigger" title="Show Log" @click="showHistory"><i class="icon icon-list-ul"></i> Show Log</button></p>

					<table width="100%" v-if="history.length">
						<thead>
							<th>Date/Time</th>
							<th>Location</th>
							<th>Temperature</th>
						</thead>

						<tbody>
							<tr v-for="log in history">
								<td>{{ log.datetime }}</td>
								<td>{{ log.location }}</td>
								<td>{{ log.currentTemperature }}</td>
							</tr>
						</tbody>
					</table>
				</section>
			</div>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import Forecast from './components/Forecast.vue';

export default {
	name: 'app',
	components: {
		Forecast
	},
	data() {
		return {
			loading: true,
			defaultLocation: 'Leipzig',
			location: 'Leipzig',
			forecast: {},
			history: []
		}
	},
	methods: {
		changeLocation() {
			// If no location entered, set default
			if (this.location.length === 0) {
				this.location = this.defaultLocation;
			}

			this.getForecast();
		},
		getForecast() {
			this.loading = true;

			// Fetch API
			axios
				.get('//localhost:3000/api/weather/forecast?location=' + this.location)
				// .get('//localhost:3000/forecast.json?location=' + this.location)
				.then((response) => {
					this.forecast = response.data;

					this.loading = false;

					this.logLocation();
				})
				.catch(() => {
					this.loading = false;
				});
		},
		logLocation() {
			let	history = JSON.parse(localStorage.getItem('history')),
					log = {
						datetime: this.$moment().format(),
						location: this.location,
						currentTemperature: this.forecast.current.temperature.current
					};

			history.push(log);

			// Push it to data if it's available
			if (this.history.length) {
				this.history.push(log);
			}

			localStorage.setItem('history', JSON.stringify(history));
		},
		showHistory() {
			this.history = JSON.parse(localStorage.getItem('history'));
		}
	},
	mounted() {
		this.getForecast();

		// Create local storage for history if it doesn't exists yet
		if (!localStorage.getItem('history')) {
			localStorage.setItem('history', JSON.stringify([]));
		}
	}
}
</script>

<style lang="scss">
	// Import selected components from Bootstrap :)
	@import '../node_modules/bootstrap/scss/functions';
	@import '../node_modules/bootstrap/scss/variables';
	@import '../node_modules/bootstrap/scss/mixins';
	@import '../node_modules/bootstrap/scss/root';
	@import '../node_modules/bootstrap/scss/reboot';
	@import '../node_modules/bootstrap/scss/type';
	@import '../node_modules/bootstrap/scss/grid';
	@import '../node_modules/bootstrap/scss/forms';
	@import '../node_modules/bootstrap/scss/buttons';
	@import '../node_modules/bootstrap/scss/spinners';
	@import '../node_modules/bootstrap/scss/utilities';

	// Icon icons
	@import 'assets/styles/icons';

	// Web font
	@import url('https://fonts.googleapis.com/css?family=Roboto:100,100i,300,300i,400,400i,500,500i,700,700i,900,900i');

	body {
		background-color: #f3f4f8;
		color: #3b3b3e;
		font-family: 'Roboto', sans-serif;
	}

	.container {
		height: 100vh;
		padding-top: 30px;
	}

	.weather {
		padding: 20px;
		min-height: 100px;
		border-radius: 10px;
		box-shadow: 0px 8px 38px -7px rgba(black, 0.1);
		background-color: white;

		// Header
		&__header {
			form {
				position: relative;
			}

			.form-control {
				padding-left: 40px;
				border: none;
				font-weight: 400;
				background-color: #f5f5f5;
			}

			.icon-search {
				position: absolute;
				top: 11px;
				left: 15px;
				color: #3b3b3e;
			}

			button {
				padding: 0;
				border: none;
				background: none;
			}
		}

		// Content
		&__content {
			display: block;
			padding: 80px 0;

			.spinner-border {
				width: 5rem;
				height: 5rem;
				border-width: 0.50em;
			}
		}

		// Forecast
		&__forecast {
			margin: 0 -20px -20px;
			border-bottom-left-radius: 10px;
			border-bottom-right-radius: 10px;
			// background-color: #f0f2f9;

			ul {
				list-style-type: none;
				margin: 0;
				padding: 0;

				li {
					display: inline-block;
				}
			}

			h4 {
				margin: 0 0 20px 0;
				font-size: 14px;
				font-weight: 400;
				text-align: center;
				text-transform: uppercase;
			}
		}

		&__day {
			width: 20%;
			padding: 20px 0;
			border-top: 1px solid #efefef;
			border-right: 1px solid #efefef;

			&:last-child {
				border-right: none;
			}
		}
	}

	.log {
		margin-top: 40px;

		&__trigger {
			border: 0;
			padding: 0;
			background: none;
			color: #cbccd4;
			font-size: 13px;
			text-transform: uppercase;
		}

		table {
			font-family: monospace;
		}
	}

	[disabled] {
		opacity: 0.5 !important;
		transition: opacity 0.2s ease-in-out;
	}

	@media only screen and (max-width: 590px) {
		.weather {
			&__day {
				width: 100%;
				border-right: none;
			}
		}
	}
</style>
