<template>
	<div class="reload" :class="{'reload--active' : scrolling, 'reload--reload' : reloading}"></div>
</template>

<script>
export default {
	props: {
		offsetY: {
			type: Number,
			default: -100
		},
		offsetYsmall: {
			type: Number,
			default: -50
		},
		smallScreen: {
			type: Number,
			default: 750
		},
		startDelay: {
			type: Number,
			default: 500
		},
		reloadDelay: {
			type: Number,
			default: 500
		},
		parentClass: {
			type: String,
			default: '.page'
		}
	},
	data() {
		return {
			timeoutTimer: function() {},
			timoutReload: function() {},
			reloading: false,
			scrolling: false,
			triggerPoint: this.$props.offsetY
		};
	},
	mounted() {
		if (process.browser) {
			// Set basic style necessary to body
			let body = document.querySelector('body');
			body.style.filter = 'blur(0px)';
			body.style.transform = 'scale(1)';
			body.style.opacity = 1;
			body.style.transition = 'filter 0.4s, opacity 0.4s, transform 0.4s';

			// Set the triggerpoint:
			this.setTriggerpoint();
			// Add the listener to scroll
			window.addEventListener('scroll', this.handleScroll);
			// Add the listener to resize
			window.addEventListener('resize', this.setTriggerpoint);
		}
	},
	methods: {
		setTriggerpoint() {
			if (window.innerWidth < this.$props.smallScreen) {
				this.triggerPoint = this.$props.offsetYsmall;
			}
		},
		handleScroll() {
			if (window.scrollY < triggerPoint) {
				this.scrolling = true;
				this.doReload();
			} else {
				this.scrolling = false;
				clearTimeout(this.timeoutTimer);
				clearTimeout(this.timeoutReload);
			}
		},
		doReload() {
			// Start the beginning of the reload.
			timeoutTimer = setTimeout(() => {
				this.reloading = true;

				// Start the Reload
				timoutReload = setTimeout(() => {
					location.reload();
				}, this.$props.reloadDelay);
			}, this.$props.startDelay);
		}
	}
};
</script>

<style scoped>
body {
	filter: blur(0px);
	transform: scale(1);
	opacity: 1;
	transition: filter 0.4s, opacity 0.4s, transform 0.4s;
}
body.fadeOut {
	opacity: 0.5;
	transform: scale(1.1);
	filter: blur(20px);
}
.reload {
	width: 100px;
	height: 100px;
	position: fixed;
	left: 50%;
	top: 0;
	transform: translateX(-50%);
}
@media screen and (min-width: 1280px) {
	.reload {
		width: 50px;
		height: 50px;
		margin-top: 10px;
	}
}

.reload:before {
	content: '';
	width: 50%;
	height: 50%;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%) scale(0) rotate(0deg);
	display: block;
	transition: transform 0.3s;
	border: 2px solid #7f7f7f;
	border-radius: 50%;
	border-bottom: 2px solid pink;
	border-top: 2px solid lightblue;
	border-left: 2px solid lightgreen;
}
@media screen and (min-width: 1280px) {
	.reload:before {
		width: 100%;
		height: 100%;
	}
}

.reload--active:before {
	transform: translate(-50%, -50%) scale(1) rotate(0deg);
	animation: rotating 1s 0.3s linear infinite;
}

.reload--reload:before {
	animation: reload 0.3s ease-in-out once;
}

@keyframes rotating {
	from {
		transform: translate(-50%, -50%) scale(1) rotate(0deg);
	}
	to {
		transform: translate(-50%, -50%) scale(1) rotate(360deg);
	}
}
@keyframes reload {
	from {
		opacity: 1;
		transform: translate(-50%, -50%) scale(1) rotate(0deg);
	}
	to {
		opacity: 0;
		transform: translate(-50%, -50%) scale(2) rotate(0deg);
	}
}
</style>