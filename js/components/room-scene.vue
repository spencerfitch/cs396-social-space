let worldCamera = undefined

AFRAME.registerComponent('rotation-reader', {
	// Track the camera's position 
	// and copy it into the user's head

  /**
   * We use IIFE (immediately-invoked function expression) to only allocate one
   * vector or euler and not re-create on every tick to save memory.
   */
	init: function () {
		console.log('Begin tracking the camera');
		worldCamera = this.el
		console.log("CAMERA:", worldCamera)
		let r = Math.random()*1 + 1
		let theta = 100*Math.random()*1 + 1
		worldCamera.object3D.position.set(r*Math.cos(theta), 1.5, r*Math.sin(theta))
		worldCamera.object3D.lookAt(0,.5,0)
		console.log("****Start pos", worldCamera.object3D.position.toArray())

		room.detailText = '';
	},
	tick: (function () {
		
		return function () {
			if (room.userHead) {
				room.userHead.position.copy(this.el.object3D.position)
				room.userHead.rotation.copy(this.el.object3D.rotation)
				room.userHead.post()
			}

		};
	})()
});

Vue.component("room-scene", {
	template: `
	<a-scene>

		<!-- Assets -->
		<a-assets>
			<img id="sunset-sky" src="textures/sunset-sky.jpg">
			<img id="mountain-albedo" src="textures/Mountain/TexturesCom_Rock_CliffMountain3_1K_albedo.png">
			<img id="mountain-ao" src="textures/Mountain/TexturesCom_Rock_CliffMountain3_1K_ao.png">
			<img id="mountain-height" src="textures/Mountain/TexturesCom_Rock_CliffMountain3_1K_height.png">
			<img id="mountain-normal">
		</a-assets>

		<!-- Camera Viewport -->
		<a-camera id="camera" rotation-reader>
			<a-cursor></a-cursor>

			<!-- Output text -->
			<a-entity>
				<a-text 
					v-if="room.userHead"
					width=".8"
					color="black"
					:value="room.userHead.position.toFixed(2)" 
					position="-.7 .7 -1">
				</a-text>
				
				<a-text 
					width="2"
					color="black"
					:value="room.titleText" 
					position="-.7 .6 -1">
				</a-text>
				<a-text 
					width="1"
					color="black"
					:value="room.detailText" 
					position="-.7 .5 -1">
				</a-text>
			</a-entity>
			
		</a-camera>

		<!-- World Terrain -->
		<obj-world :room="room"/>

		<a-entity position="0 0 0">
			<a-entity text="value:hello;font:/fonts/helvetica-sdf.fnt; fontImage:/fonts/helvetica-sdf.png;width:10;color:black" position="0 1 0"></a-entity>
			
			<!--------- ALL THE OBJECTS YOU'VE MADE --------->
			<live-object  v-for="obj in room.objects" :key="obj.uid" :obj="obj" />
		</a-entity>


	</a-scene>`,

	methods: {
		camtick() {
			console.log("cam")
		}
	},
	mounted() {
		// Create
	},

	data() {
		return  {
			
		}
	},

	props: ["room"],
})