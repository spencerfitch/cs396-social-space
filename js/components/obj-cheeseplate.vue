Vue.component("obj-cheeseplate", {
	template: `
	<a-entity>
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

    <a-cylinder
      position="0 0 0"
      radius="0.5"
			@click="click"
      :height="this.obj.height"
      :color="this.obj.color.toHex()"></a-cylinder>

    <a-light
			:animation="intensityAnimation"

			position="0 1 0"
			intensity="2"
			:color="obj.color.toHex()"
			type="point"
			:distance="10"
			decay="2"></a-light>
	</a-entity>
	`,

	// Values computed on the fly
	computed: {
		intensityAnimation() {
			return `property: intensity; from:.3; to:.6; dir:alternate;dur: 500; easing:easeInOutQuad;loop:true`
		},
	},

	methods: {
		click() {
      this.obj.height += 0.5;
      this.obj.heightSpeed = 0;

			// Tell the server about this action
			this.obj.post()
		}
	},

	// this function runs once when this object is created
	mounted() {
    this.obj.onUpdate = function({t, dt, frameCount}) {
      Vue.set(this.color.v, 0, (noise(t*0.02)+1)*180);

      if (this.height > this.heightMin) {
        const correctDt = -dt;
        this.heightSpeed += this.heightAccel*correctDt
        this.height = Math.max(this.heightMin, this.height - this.heightSpeed*correctDt);
      }
    }
	},

	props: ["obj"],
    
})