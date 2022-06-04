
Vue.component("obj-world", {

	template: `
	<a-entity>
		<!--------- SKYBOX --------->
		<a-sky color="lightblue"></a-sky>

		<a-plane 
			roughness="1"
			shadow 
			color="hsl(140,40%,40%)"
			height="100" 
			width="100" 
			rotation="-90 0 0">
		</a-plane>

		<!---- lights ----> 
		<a-entity light="type: ambient; intensity: 0.4;" color="white"></a-entity>
		<a-light type="directional" 
			position="0 0 0" 
			rotation="-90 0 0" 
			intensity="0.4"
			castShadow target="#directionaltarget">
			<a-entity id="directionaltarget" position="-10 0 -20"></a-entity>
		</a-light>

		<a-cone 
			v-for="(tree,index) in trees"
			:key="'tree' + index"
			shadow 

			:color="tree.color.toHex()"
			:base-radius="tree.size.z" 
			:height="tree.size.y" 

			segments-radial=10
			segments-height=1
			
			:rotation="tree.rotation.toAFrame()"
			:position="tree.position.toAFrame()">
		</a-cone>

		

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
		// everyone will have the same view. (Wordle-style!)
		let trees = []
		let count = 30
		for (var i = 0; i < count; i++) {
			let h = 6 + 4*noise(i) // Size from 1 to 3
			let tree = new LiveObject(undefined, { 
				size: new THREE.Vector3(.3, h, .3),
				color: new Vector(noise(i*50)*30 + 160, 100, 40 + 10*noise(i*10))
			})
			let r = 20 + 10*noise(i*40)
			let theta = 2*noise(i*10)
			tree.position.setToCylindrical(r, theta, h/2)
			tree.lookAt(0,1,0)
			trees.push(tree)
		}

		let rocks = []
		let rockCount = 20
		for (var i = 0; i < rockCount; i++) {
			let h = 1.2 + noise(i*100) // Size from 1 to 3
			let rock = new LiveObject(undefined, { 
				size: new THREE.Vector3(h, h, h),
				color: new Vector(noise(i)*30 + 140, 0, 40 + 20*noise(i*3))
			})
			let r = 4 + 1*noise(i*1)
			// Put them on the other side
			let theta = 2*noise(i*10) + 3
			rock.position.setToCylindrical(r, theta, h*.3)
			// Look randomly
			rock.lookAt(Math.random()*100,Math.random()*100,Math.random()*100)
			rocks.push(rock)
		}


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
