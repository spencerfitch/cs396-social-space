
Vue.component("obj-world", {

	template: `
	<a-entity>
		<!-- Skybox -->
		<a-sky src="textures/sunset-sky.jpg" phi-start="90"></a-sky>

		<!---- Sunlight ----> 
		<a-entity
			position="-80 240 -400"
			geometry="primitive: sphere; radius: 40;"
			material="color: #FD6E63"
			light="type: directional; color: #FD5E53; castShadow: true;"></a-entity>
		<!-- Base ambient light-->
		<a-entity light="type: ambient; color: #ccc"></a-entity>

		<!-- Ground -->
		<a-plane 
			roughness="1"
			shadow 
			color="hsl(172,62%,29%)"
			height="400" 
			width="400" 
			rotation="-90 0 0">
		</a-plane>

		<!-- Mountains -->
		<a-entity position="0 0 -240" scale="80 120 40">
			<a-entity
				position="0 .6 0"
				geometry="primitive: cone; radiusBottom: 1.75; radiusTop: 0.1; height: 1.5;"
				material="
					src: #mountain-albedo;
					ambientOcclusionMap: #mountain-ao;
					normalMap: #mountain-normal;
					displacementMap: #mountain-height;
					displacementBias: -0.3;
					displacementIntensity: 0.5;
					roughness: 0.9;"></a-entity>
			<a-entity
				position="1 .3 0"
				rotation="0 -90 0"
				geometry="primitive: cone; radiusBottom: 2; radiusTop: 0.1;"
				material="
					src: #mountain-albedo;
					ambientOcclusionMap: #mountain-ao;
					normalMap: #mountain-normal;
					displacementMap: #mountain-height;
					displacementBias: -0.3;
					displacementIntensity: 0.5;
					roughness: 0.9;"></a-entity>
			<a-entity
				position="-2 .3 0"
				rotation="0 -90 0"
				geometry="primitive: cone; radiusBottom: 2; radiusTop: 0.1;"
				material="
					src: #mountain-albedo;
					ambientOcclusionMap: #mountain-ao;
					normalMap: #mountain-normal;
					displacementMap: #mountain-height;
					displacementBias: -0.3;
					displacementIntensity: 0.5;
					roughness: 0.9;"></a-entity>
			<a-entity
				position="-1 .175 1"
				rotation="0 -90 0"
				geometry="primitive: cone; radiusBottom: 1; radiusTop: 0.1; height: .75"
				material="
					src: #mountain-albedo;
					ambientOcclusionMap: #mountain-ao;
					normalMap: #mountain-normal;
					displacementMap: #mountain-height;
					displacementBias: -0.3;
					displacementIntensity: 0.5;
					roughness: 0.9;"></a-entity>
		</a-entity>

		<!-- Trees -->
		<a-entity
			v-for="(tree, index) in trees"
			:position="tree.position.toAFrame()"
			:scale="tree.size"
		>
			<!-- Leaves -->
			<a-entity position="0 0.7 0">
				<a-entity
					position="0 0 0"
					geometry="primitive: cone; radiusBottom: 0.7;"
					material="color: #376628; roughness: .9;"></a-entity>
				<a-entity
					position="0 .35 0"
					geometry="primitive: cone; radiusBottom: 0.6; height: 0.9;"
					material="color: #376628; roughness: .9;"></a-entity>
				<a-entity
					position="0 .7 0"
					geometry="primitive: cone; radiusBottom: 0.45; height: 0.7;"
					material="color: #376628; roughness: .9;"></a-entity>
			</a-entity>

			<!-- Trunk -->
			<a-entity
				position="0 0.5 0"
				geometry="primitive: cylinder; radius: 0.12;"
				material="color: #6F432A"></a-entity>
		</a-entity>
		
		<a-box 
			v-for="(rock,index) in rocks"
			:key="'rock' + index"
			shadow 

			roughness="1"

			:color="rock.color.toHex()"
			:width="rock.size.x" 
			:depth="rock.size.z" 
			:height="rock.size.y" 
			
			:rotation="rock.rotation.toAFrame()"
			:position="rock.position.toAFrame()">
		</a-box>

	</a-entity>
		`,

	data() {
		// Where we setup the data that this *rendered scene needs*

		// EXAMPLE: Generated landscape
		// Make some random trees and rocks
		// Create a lot of LiveObjects (just as a way 
		//  to store size and color conveniently)
		// Interpret them as whatever A-Frame geometry you want!
		// Cones, spheres, entities with multiple ...things?
		// If you only use "noise" and not "random", 
		// everyone will have the same view. (Wordle-style!)s
		
		const trees = [...Array(70).keys()].map(i => {
			let w = 2 + noise(i);
			let h = 2.5 + noise(i);
			let tree = new LiveObject(
				undefined,
				{
					size: new THREE.Vector3(w, h, w),
				},
			);

			let r = 10 + 5*noise(i*10);
			let theta = 1.5*Math.PI*noise(i*800) + Math.PI/2;

			tree.position.setToCylindrical(r, theta, 0);
			tree.lookAt(0,1,0);
			return tree;
		});

		const rocks = [...Array(20).keys()].map(i => {
			const h = 0.75 + 0.75*noise(i*100) // Size from 1 to 3
			const rock = new LiveObject(undefined, { 
				size: new THREE.Vector3(h, h, h),
				color: new Vector(noise(i)*30 + 140, 0, 40 + 20*noise(i*3))
			})
			const r = 4 + 1*noise(i*1)
			// Put them on the other side
			const theta = Math.PI/2*noise(i*10) + Math.PI/2
			rock.position.setToCylindrical(r, theta, h*.3)
			// Look randomly
			rock.lookAt(Math.random()*100,Math.random()*100,Math.random()*100);
			return rock;
		});

		return {
			trees: trees,
			rocks: rocks
		}
	},

	mounted() {
		// Create a fire object
		// Attach this liveobject to the ROOM
		// and then the room deals with drawing it to AFRAME
		let fire = new LiveObject(this.room, {
			paritype: "fire",  // Tells it which type to use
			uid: "fire0",
			isTracked: true,
			onUpdate({t, dt, frameCount}) {
				// Change the fire's color
				let hue = (noise(t*.02)+1)*180
				Vue.set(this.color.v, 0, hue)
			}
		})
	

		fire.position.set(0, 0, 0)
		fire.fireStrength = 1

		// let fire2 = new LiveObject(this.room, {
		// 	paritype: "fire",  // Tells it which type to use
		// 	uid: "fire2",
		// 	onUpdate({t, dt, frameCount}) {
		// 		let hue = (noise(t*.02)+1)*180
		// 		Vue.set(this.color.v, 0, hue)
				
		// 		// console.log(this.color[0] )
		// 	}
		// })

		// fire2.position.set(3, 0, -4)
		// fire2.fireStrength = 7

		
		let grammar = new tracery.createGrammar(  {
			songStyle : ", played as #song.a#, on #musicModifier# #instrument#",
			instrument : ["ukulele", "vocals", "guitar", "clarinet", "piano", "harmonica", "sitar", "tabla", "harp", "dulcimer", "violin", "accordion", "concertina", "fiddle", "tamborine", "bagpipe", "harpsichord", "euphonium"],
			musicModifier : ["heavy", "soft", "acoustic", "psychedelic", "light", "orchestral", "operatic", "distorted", "echoing", "melodic", "atonal", "arhythmic", "rhythmic", "electronic"],
			musicGenre : ["metal", "electofunk", "jazz", "salsa", "klezmer", "zydeco", "blues", "mariachi", "flamenco", "pop", "rap", "soul", "gospel", "buegrass", "swing", "folk"],
			musicPlays : ["echoes out", "reverberates", "rises", "plays"],
			musicAdv : ["too quietly to hear", "into dissonance", "into a minor chord", "changing tempo", "to a major chord", "staccatto", "into harmony", "without warning", "briskly", "under the melody", "gently", "becoming #musicGenre#"],
			song : ["melody", "dirge", "ballad", "poem", "beat poetry", "slam poetry", "spoken word performance", "hymn", "song", "tone poem", "symphony"],
			musicAdj : ["yielding", "firm", "joyful", "catchy", "folksy", "harsh", "strong", "soaring", "rising", "falling", "fading", "frantic", "calm", "childlike", "rough", "sensual", "erotic", "frightened", "sorrowful", "gruff", "smooth"],
        
		}, {})
		grammar.addModifiers(baseEngModifiers)

		const campfireSongs = ["Lonely Goatherd", "On top of spaghetti", "Princess Pat", "BINGO", "Old Mac Donald", "Going on a Bear Hunt", "The Green Grass Grew All Around", "Home on the Range", "John Jacob Jingleheimer Schmitt", "The Wheels on the Bus", "If I had a Hammer"]
		this.room.detailText = "Campfire time!"

		this.room.time.onSecondChange((second) => {
			// Change the song every minute (60 seconds)
			let rate = 10 // How many seconds between changes
			if (second%rate === 0) {
				let tick = second/rate
				let index = second % campfireSongs.length
				let song = campfireSongs[index]
				this.room.detailText =  song + grammar.flatten("#songStyle#")
			}
		})
	},

	props: ["room"]

});
