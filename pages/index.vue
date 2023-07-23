<template>
  <div id="canvas">
    <div id="deep-space" />
    <div id="space-field">
      <SpaceObject id="spaceship" class="spaceship" :data="spaceField.ship" resolution="2" />

      <SpaceObject class="asteroid" :data="asteroid" resolution="2"
                  :key="asteroid.center"
                  v-for="asteroid in spaceField.asteroids" />

      <SpaceObject class="missile" :data="missile" resolution="2"
                  :key="missile.center"
                  v-for="missile in spaceField.missiles" />

      <SpaceObject class="explosion" :data="explosion" resolution="2"
                  :key="explosion.center"
                  v-for="explosion in spaceField.explosions" />
      </div>

    <div id= "menu">
      <h1 @click="click(1)"> Start Game</h1>
      <h1 @click="click(2)">LeaderBoard</h1>
      <h1 @click="click(3)">Quit</h1>
    </div> 
    </div>
</template>

<script setup >

  import json from './json/data.json'
  const {
    data: spaceField,
    refresh: updateSpaceField
  } = await $get("/space-field");

  onMounted(() => {
    window.addEventListener("keydown", async (event) => {
      const keyToCommand = {
        "ArrowUp": "MOVE_SHIP_UP",
        "ArrowDown": "MOVE_SHIP_DOWN",
        "ArrowRight": "MOVE_SHIP_RIGHT",
        "ArrowLeft": "MOVE_SHIP_LEFT",
        "Space": "LAUNCH_MISSILE",
        "Escape": "PAUSE_GAME",
      };

      const command = keyToCommand[event.code];

      // Ignore if invalid key was pressed
      if (command === undefined) return;

      console.log(`Triggering command: ${command}`);
      await $post("/ship/commands", { command })
    });

    window.setInterval(updateSpaceField, 100);
  })



  function click(i){
      alert("option " + i);
      switch(i){
        case 1:
          alert("start !")
          var menu = document.getElementById("menu");
          menu.style.visibility='hidden';
          break;
        case 2:
          alert("1º: " + json[0].name+", score: "+json[0].score + ";\n" +
                "2º: " + json[1].name+", score: "+json[1].score + ";\n" +
                "3º: " + json[2].name+", score: "+json[2].score + ";\n"
            )
          break;
        case 3:
          alert("closing Game");
          // Chrome error: Scripts may close only the windows that were opened by them.
          // Firefox config: dom.allow_scripts_to_close_windows =true
         window.close();
         break;     
      }
  }

</script>

<style>
#canvas {
  height: calc(100vh - 4rem);
  width: calc(100vw - 4rem);

  padding: 2rem;

  background-color: #36bbf5;
  overflow: hidden;

  position: relative;

  display: flex;
  justify-content: center;
  align-items: center;
}

@keyframes slide {
  0% {
    transform: translate(1px);
  }
  50% {
    transform: translate(-1px);
  }
  100% {
    transform: translate(1px);
  }
}

#deep-space {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  /*background-image: url("~/assets/space.png");*/
  background-origin: content-box;
  animation: slide 3s linear infinite;

  position: absolute;
  z-index: 0;
}

#menu {
  height: 80px;
  width:  160px;
  position: absolute;
  background-color: white;
}

h1:hover{
  background-color: yellow;
}


#space-field {
  height: calc(100% - 4rem);
  width: calc(100% - 4rem);

  position: relative;
}

.spaceship {
  background-image: url("~/assets/spaceship.png");
}

.asteroid {
  background-image: url("~/assets/asteroid.png");
}

.missile {
  background-image: url("~/assets/missile.png");
}

.explosion {
  background-image: url("~/assets/explosion.png");
}
</style>
