
Vue.component("obj-head", {
	template: `
	<a-entity>
		<!-- Head -->
		<a-sphere 
			shadow
			:radius="headSize"
			:color="obj.color.toHex()" 
				
			>
			<obj-axes scale=".1 .1 .1" v-if="false" />
		</a-sphere>

		<!-- Nose -->
		<a-cone
			:height="headSize*.6"
			:radius-bottom="headSize*.4"
			position="0 0 -.18"
			
			:color="obj.color.toHex(.3)" 
		></a-cone>

		<a-torus-knot
			position="0 0.3 0"
			scale="0.1 0.1 0.1"
			segments-radial="24"
			segments-tubular="300"
			:color="obj.color.toHex(.3)"
			:p="hat.p"
			:q="hat.q"
			:radius-tubular="hat.radiusTubular"></a-torus-knot>
	
	</a-entity>
	`,
	computed: {
		color() {
			return this.obj.color.toHex?this.obj.color.toHex():this.obj.color
		},
		headSize() {
			return this.obj.size instanceof Vector ? this.obj.size.x : this.obj.size
		},
	},

	data() {
		const hat = new LiveObject(
			undefined,
			{
				radiusTubular: Math.random() * 0.2 + 0.05,
				p: Math.floor(10 * Math.random()) + 1,
				q: Math.floor(10 * Math.random()) + 2,
			},
		);
		hat.position.set(0, 1, 0);
		hat.lookAt(0, 1, 0);

		return {
			hat: hat
		}
	},

	mounted() {
		// console.log(this.headSize)
	},
	props: ["obj"]
});
