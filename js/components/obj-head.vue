
Vue.component("obj-head", {
	template: `<a-entity>

		<a-sphere 
			shadow
			:radius="headSize"
			:color="obj.color.toHex()" 
				
			>
			<obj-axes scale=".1 .1 .1" v-if="false" />
		</a-sphere>

		<a-cone v-for="(spike,index) in spikes"
			:key="index"
			:height="spike.size"
			:radius-bottom="headSize*.2"
			:position="spike.position.toAFrame(0, .2, 0)"
			:rotation="spike.rotation.toAFrame()"
			:color="obj.color.toHex(.5*Math.sin(index))" 
				
			>
		
		</a-cone>

		<!-- NOSE -->
		<a-cone
		
			:height="headSize*.6"
			:radius-bottom="headSize*.4"
			position="0 0 -.18"
			
			:color="obj.color.toHex(.3)" 
			
		>
	
		</a-cone>
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
		let spikeCount = Math.random()*10 + 10
		let spikes = []
		let h2 = Math.random() - .5
			
		for (var i = 0; i < spikeCount; i++) {
			let h = .1
			let spike = new LiveObject(undefined, { 

				size: Math.random()*.4 + .2,
				color: new Vector(noise(i)*30 + 140, 0, 40 + 20*noise(i*3))
			})
			let r = .2
			// Put them on the other side
			let theta = 4*noise(i*10) + 3
			spike.position.setToCylindrical(r, theta, h*.3)
			// Look randomly
			spike.lookAt(0, h2, 0)
			spike.rotateX(-Math.PI/2)
			spikes.push(spike)
		}

		return {
			spikes: spikes
		}
	},

	mounted() {
		// console.log(this.headSize)
	},
	props: ["obj"]
});
