Vue.component("obj-bird", {
	template: `
	<a-entity>
    <!-- Wings -->
    <a-box
      position="0.5 0 0"
      rotation="0 10 0"
      height="0.1"
      width="0.8"
      depth="0.4"
      color="#FFF"></a-box>
    <a-box
      position="-0.5 0 0"
      rotation="0 -10 0"
      height="0.1"
      width="0.8"
      depth="0.4"
      color="#FFF"></a-box>

    <!-- Body -->
    <a-box
      position="0 0 0"
      height="0.1"
      width="0.3"
      depth="1"
      color="#FFF"></a-box>
	</a-entity>
	`,

	props: ["obj"],
    
})