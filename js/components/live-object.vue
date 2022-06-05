/*
 * Data that is connected *live*
 * What happens when we get a position from online (like a new body part)?
 * Hopefully, we heard about that as part of an event? or part of a value change?
 */

Vue.component("live-object", {
	template: `<a-entity>
	
		<a-text 
			v-if="obj.label && !isUser"
			:value="obj.label"
			:width="obj.labelWidth || 1"
			:color="obj.labelColor||obj.labelColor||'black'"
			:position="obj.position.toAFrame(0,.2 + (obj.size.y||obj.size),0)"
			:rotation="obj.cameraFacing.rotation.toAFrame()"
			>
		</a-text>

		<component :is="'obj-' + (obj.paritype || 'cube')" 
			v-if="!isUser"
			:obj="obj" 
			:position="obj.position.toAFrame(0,0,0)" 
			:rotation="obj.rotation.toAFrame()" />

		<!-- Mirror test! -->
		<a-entity v-else scale="1 1 -1">
			<component :is="'obj-' + (obj.paritype || 'cube')" 
				v-if="mirrorself"
				:obj="obj" 
				:position="obj.position.toAFrame(0,0,mirrorself)" 
				:rotation="obj.rotation.toAFrame()" />
		</a-entity>
	</a-entity>`,
	computed: {
		isUser() {
			// console.log("Is user", this.obj.room.userHead === this.obj, this.objuid)
			return this.obj.room.userHead == this.obj
		}
	},
	data() {
		return {
			mirrorself: parseFloat(params.mirrorself || 0)
		}
	},
	props: ["obj"]
});
