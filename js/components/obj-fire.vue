Vue.component("obj-fire", {
	template: `
	<a-entity>
		<obj-axes scale="5 5 5" v-if="false" />
		<a-sphere 
			color="grey"
			radius=2 
			scale="1 .3 1" 
			roughness=1
			segments-height="5"
			segments-width="10"
			theta-start=0
			theta-length=60
			position="0 -.4 0"
			>
		</a-sphere>
		<a-cone
			position="0 .2 0"
			@click="click"
			:animation="heightAnimation"
			:color="obj.color.toHex()"
			height=.2
			radius-bottom=".2"

			:scale="(obj.fireStrength*.2 + 1) + ' ' + .1*obj.fireStrength + ' ' + (obj.fireStrength*.2 + 1)"
			:material="fireMaterial">

		</a-cone>

		<a-light
			:animation="intensityAnimation"

			position="0 1 0"
			intensity="2"
			:color="obj.color.toHex()"
			type="point"
			:distance="obj.fireStrength*4 + 10"
			decay="2">
		</a-light>
	</a-entity>

	`,

	// Values computed on the fly
	computed: {
		fireMaterial() {
			return `emissive:${this.obj.color.toHex(.2)}`
		},
		
		animationSpeed() {
			return 500
		},
		intensityAnimation() {
			return `property: intensity; from:.3; to:.6; dir:alternate;dur: ${this.animationSpeed}; easing:easeInOutQuad;loop:true`
		},
		heightAnimation() {
			return `property: height; from:${this.obj.fireStrength};to:${this.obj.fireStrength*2}; dir:alternate;dur: 500; easing:easeInOutQuad;loop:true`
		}
	},

	methods: {
		click() {
			this.obj.fireStrength += 1
			this.obj.fireStrength = this.obj.fireStrength%10 + 1

			// Tell the server about this action
			this.obj.post()
		}
	},

	// this function runs once when this object is created
	mounted() {

	},

	props: ["obj"],
    
})