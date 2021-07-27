<template>
<div id="app" style="display: grid;">

  <!--########################################### HEADER ###########################################-->


  <div class="grid-container">
    <div class="item1">
      <div v-if="activate">
        <h1>
          <b style="font-size: 3.6rem;">
          Cookies: {{ metric(cookie, 4, 12, true, 0) }}
          </b>
        </h1>
        <h4 v-if="lumen <= 0">Production: {{ metric(dcClick, 2, 9, true) }} c./click & {{ metric(dcSec, 2, 9, true) }} c./sec</h4>
        <div v-else>
          <h3 class="lumen-text">
            Lumenyte: {{ metric(lumen) }}
          </h3>
        </div>
      </div>
      
      <div v-else>
        
      </div>
    </div>


    <!--###########################################  UPPER LEFT ###########################################-->


    <div class="item2 scrollbar-div" style="padding-right: 0; padding-bottom: 0;">
      <h3>
        <strong>Production Upgrades</strong>
      </h3>
      <ul style="list-style-type:none; padding-left: 0;">
        <li v-for="item of upgrades" :key="item.name">
          <div v-if="activate || !item.cost">
            <button :disabled="cookie < item.cost" 
            :class="(cookie < item.cost) ? 'locked upgrade-button': 'unlocked upgrade-button'" 
            type="upgrade" @click="item.upgradeFunc()" 
            v-if="sum >= item.unlock || showUps">
              <div class="upgrade-grid">
                <div class="grid-button-upper">
                  <h3 style="font-size: 2rem; text-align: left; width: 25vw; padding-left: 0.5rem; 
                  transform: translateY(-0.2rem);"><b>
                  {{ item.name }}
                  </b></h3>
                </div>
                <div class="grid-button-lower">
                  <h3 style="font-size: 1.25rem; text-align: left; width: 25vw; padding-left: 0.5rem; opacity: 80%;
                   transform: translateY(-0.2rem);">
                  Cost: {{ metric(item.cost, ) }} c. ~{{ rate ? metric(Math.max(0, (item.cost - cookie))/rate, 2, 3) : 0}}min <br>
                  {{item.description ? item.description : ''}}
                  </h3>
                </div>
                <div class="grid-button-right">
                  <h3 style="font-size:2.7rem; transform: scale(1.2); padding-right: 0.4rem" 
                  :style="(item.level < item.max) ? {'color': 'white'} : {'color': 'lime'}">
                    &nbsp;<b>{{ formatDec(item.level) }}</b>
                  </h3>
                </div>
              </div>
            </button>

            <button v-else :disabled="true" class="locked upgrade-button">
              <h3 style="font-size: 1.5rem">
                Unlocks at {{ metric(item.unlock, 2, 9, true, 0) }} cookies
              </h3>
            </button>
          </div>
        </li>
      </ul>
    </div> 


    <!--###########################################  CENTER ###########################################-->

    <div class="item3 center">
      <h1>
      <button type="cookieClick" :disabled="!activate" 
      :class="(activate) ? 'unlocked cookie-button' : 'cookielocked style='" 
      style="background: none; border: none; "
       @click="clickCookie">
        <img src="./assets/cookie.png" style="height: 24rem; width: 24rem; z-index: 2" draggable=false>
      </button>
      </h1>
    </div>    


    <!--###########################################  UPPER RIGHT ###########################################-->


    <div class="item4 scrollbar-div">
      <h3>
        <strong>System Modules</strong>
      </h3>
      <ul style="list-style-type:none; padding-left: 0;">
        <li v-for="item of mods" :key="item.name">
          <div v-if="activate">
            <button :disabled="(cookie < item.cost_cookie || lumen < item.cost_lumen) || isTraveling" 
            :class="(cookie < item.cost_cookie || lumen < item.cost_lumen) || isTraveling
            ? 'locked upgrade-button module-button':
             'unlocked upgrade-button module-button'" 
            type="upgrade" @click="item.upgradeFunc()"
            v-if="(item.show && sum >= item.unlock) || showUps">

              <div class="upgrade-grid">
                <div class="grid-button-upper">
                  <h3 style="font-size: 2rem; text-align: left; padding-left: 0.5rem; transform: 
                  translateY(-0.2rem);"><b>
                    {{item.name}}
                  </b></h3>
                </div>
                <div class="grid-button-lower">
                  <h3 style="font-size: 1.25rem; text-align: left; padding-left: 0.5rem; opacity: 80%; 
                  transform: translateY(0rem);">
                    <div style="display:inline" v-if="item.cost_cookie">
                      Cost: {{ item.cost_cookie ? metric(item.cost_cookie) : 0}} c.
                    </div>
                    <div style="display:inline" class="lumen-text" v-if="item.cost_lumen">
                      {{ metric(item.cost_lumen) }} lumen
                    </div>
                    <div style="display:inline">
                    ~{{ rate ? metric(Math.max(0, (item.cost_cookie - cookie))/rate, 2, 3) : 0}}min
                    </div>
                    <div v-if="item.description">
                      {{item.description}}
                    </div>
                  </h3>
                </div>
              </div>
            </button>
            <button v-else-if="item.show || showUps" :disabled="true" class="locked upgrade-button module-button"
                    style="padding: 0">
              <h3 style="font-size: 1.5rem;">
                Unlocks at {{ metric(item.unlock, 2, 9, true, 0) }} cookies
              </h3>
            </button>
          </div>
        </li>
      </ul>
    </div>


    <!--###########################################  LOWER RIGHT ###########################################-->


    <div class="item5">
      <h3 style="font-family: JetBrains Mono; margin:0.5em">
        <strong>System Logs</strong>
      </h3>
      <hr style="border-color: hsl(0, 0%, 30%); margin:0">
      <div style="text-align: left;">
        <div class="scrollbar-div" style="height: calc(40vh - 7rem);"
        id="terminal-div">
          <ul style="list-style-type: none; padding-left: 0;">
            <li v-for="(msg, index) of terminal" :key="`fruit-${index}`"
            class="terminal-msg" style="">
              <h4 class="terminal-text">
                <b>></b> {{msg}}
              </h4>
            </li>
            <li style="terminal-msg">
              &nbsp;
            </li>
          </ul>
        </div>

        <div class="" style="display: flex; width: 20vw; font-style: italic;">
          <h3 style="padding: 0.25rem; display: inline; justify-self: center;" class="terminal-text">
            <b>{{ name }}$</b>
          </h3>
          <div style="background:none; width:21vw;">
            <input type="console-input" name="console-input" class="console-input"
            style="font-style: italic"
            v-model="terminalInputContent">
          </div>
        </div>
      </div>
    </div>


    <!--########################################### FOOTER ###########################################-->
    

      <div class="item6"> 
        <div v-if="mods.navigation.level" class="nav-grid">
          <div class="nav-upper">
            <div style="width:50%;margin-right:0.5rem">
              <h3>
                <i>Current location: </i> <br>
                <b>{{ loc_system.name }}</b> {{ loc_planet.number }}
              </h3>
            </div>
            <div style="width:50%; margin-left:0.5rem;">
              <h3>
                <i>Planet type: </i><br>
                <b>{{ loc_planet.dispname }}</b>
              </h3>
            </div>
          </div>
          <div class="nav-middle">
            <div v-if="loc_planet.surface.scanned" style="place-self:center">
              <button v-if="loc_planet.surface.scanned" 
              :disabled="lumenDroneActive || loc_planet.surface.lumen_deposit <= 0.005" 
              :class="lumenDroneActive || loc_planet.surface.lumen_deposit <= 0.005
              ? 'locked upgrade-button module-button' : 'unlocked upgrade-button module-button'" 
              @click="lumenMining()" style="width: 28vw">

                <div class="upgrade-grid">
                  <div class="grid-button-upper" style="height: 150%; line-height: 0.9  em; padding-top: 1.4em;">
                    <strong class="lumen-text" style="font-size: 1.5rem" v-if="loc_planet.surface.lumen_deposit > 0.005">
                      Lumenyte deposits found ( {{ metric(loc_planet.surface.lumen_deposit) }} units remaining )
                    </strong>
                    <strong v-else class="lumen-text" style="font-size: 1.5rem">
                      Lumenyte deposits fully depleted
                    </strong>
                  </div>
                  <div class="grid-button-lower" style="font-size: 1rem">
                    <h3>Begins lumenyte surface extraction operations</h3>
                  </div>
                </div>
              </button>
            </div>
            <strong v-else style="font-style: italic; opacity: 70%; font-size: 1.7rem; place-self:center">
              Surface information not available
            </strong>
          </div>
          <div class="nav-lower">
          </div>
          <div class="nav-disp">
            <img :src="getImgUrl(loc_planet.icon)" style="width: 8vw">
          </div>
          <div class="nav-bar">
            <div class="unlocked map-button" @click="dispmap = true; dispinfo = {}">
              Map
            </div>
          </div>
        </div>
        <div v-else>
          <h3>
            <strong>NAVIGATIONS OFFLINE</strong>
          </h3>
        </div>
      </div>


    <div v-if="dispmap" class="map"                
      :style="dispclosing ? 'animation: map-close .3s ease forwards;' : ''" >
      <div class="map-header" style="" >
        <div class="locked map-button" style="width: 9vw;  margin-left: 1.2rem ;">
          Interstellar map
        </div>
        <div style="display:flex; flex-direction:column;">
          <strong style="justify-self: center; font-size: 2rem;">SYSTEM MAP</strong>
          <h3 style="font-size:1.25rem; font-style:italic;">- {{ loc_system.name }} -</h3>
        </div>
        <div class="unlocked map-button" style="width: 9vw; margin-right: 1.2rem ;" @click="closeMap()">
          Close
        </div>
      </div>
      <div class="map-left scrollbar-div">
        <ul style="list-style-type:none; padding-left: 0;">
          <li v-for="body of loc_system.bodies" :key="body.number">
            <div style="opacity: 0%;" 
            :style="'animation: desc-slide 0.25s ease forwards; animation-delay: ' + (body.number*0.1 + 0.2) + 's'">
              <div class="planet-div">
                <div style="display: inline">
                  <button class="unlocked planet-button" 
                  style="padding: 0; display: grid;" @click="dispinfo = body" >
                    <img :src="getImgUrl(body.icon)" style="width: 6vw; place-self:center">
                  </button>
                </div>
                <img src="./assets/arrow.png" v-if="dispinfo.number == body.number" 
                        style="place-self: center; width: 2vw; transform: translateX(-1vw)">
                <h3 style="width: 12vw; display: flex; flex-direction: column;">
                  <i style="font-size: 1.25rem; color: hsl(0, 0%, 80%)">
                    {{ loc_system.name }} {{ body.number ? body.number : "star"}}
                  </i>                
                  <strong style="font-size:2rem; ">
                    {{ body.dispname }}
                  </strong>
                  <h4 style="font-size: 1.5rem">
                    <div v-if="loc_planet.number != body.number">
                      {{ metric(calcDistanceCost(loc_planet.number, body.number)) }} c. |
                      {{ metric(calcTimeCost(loc_planet.number, body.number), 2, 4, 1, 0) }}s
                    </div>
                    <i v-else class="notice-blinking">
                      Current location
                    </i>
                  </h4>
                </h3>
              </div>
              <div class="vert-line" style="transform: translateX(4vw);"></div>
            </div>
          </li>
        </ul>
      </div>
      <div class="map-right scrollbar-div" style="padding: 1rem">
        <div v-if="dispinfo.name" :key="dispinfo.number" 
        style=" display:flex; flex-direction: column; justify-content: space-between;
         height: 100%">
          <div>
            <div class="desc-header desc-anim1" 
            :style="dispinfo.banner ? 
            { backgroundImage: 'url(' + getImgUrl(`${dispinfo.banner}`) + ')' } :
            'background-color: ' + planet_pool[dispinfo.category][dispinfo.name].color"
            style="width: 16vw;  z-index: 0; border-radius: 4vw;
              background-size: cover; background-repeat: no-repeat; ">
              <div style="display: flex; transform: translate(-0.22rem, -0.22rem);">
                <div class="desc-header" style="width: 4vw; height: 4vw; border-radius:4vw; background: black;
                            z-index: 2; align-self: center; transform: translateY(0.33rem);">
                  <img :src="getImgUrl(dispinfo.icon)" style="height: 4vw; width: 4vw;">
                </div>
                <div style="align-self: center; margin-left:0.4rem; font-size: 1.62rem; opacity: 0%; z-index: 1;
                                animation: desc-slide 0.25s ease forwards; animation-delay: .45s;
                                text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000, 1px 1px 0 #000;">
                  <h3 style="width: 12vw; display: flex; flex-direction: column;">
                    <i style="font-size: 1.3rem; color: hsl(0, 0%, 85%)">
                      {{ loc_system.name }} {{ dispinfo.number ? dispinfo.number : "star"}}
                    </i>                
                    <strong style="font-size:1.7rem; ">
                      {{ dispinfo.dispname }}
                    </strong>
                  </h3>
                </div>
              </div>  
              
            </div>
            

            <div style="font-size: 1.2rem; animation-delay: .9s; opacity: 0%;
            " class="desc-slide">
              <p>
                {{ planet_pool[dispinfo.category][dispinfo.name].description }} <br> {{ dispinfo }}
              </p>
              <div v-if="dispinfo.surface">
                <i>Surface has been mapped</i>
                <div v-if="dispinfo.surface.lumen_deposit">
                  <p>Lumenyte deposits found ( {{ metric(loc_planet.surface.lumen_deposit) }} units remaining )</p>
                </div>
              </div>
              <div v-else class="notice-blinking">
                <i>Surface scan not available</i>
              </div>
            </div>
          </div>

          <div style="place-self: center; width:60%;">
            <button :disabled="!mods.propulsion.level || isTraveling"
            :class="mods.propulsion.level && !isTraveling ? 'unlocked upgrade-button' : 'locked upgrade-button'" 
            style="width: 100%;font-size:2rem; line-height: 1.5rem; padding: 2rem;"
            @click="intrasystemTravel(dispinfo)">
              <strong>Travel to</strong> <br>
            </button>
          </div>

        </div>
        <div v-else style="text-align:center;" class="notice-blinking">
          <i>Select a planet to see description</i>
        </div>
      </div>
    </div>


    <!--###########################################  LOWER LEFT ###########################################-->


    <div class="item7">
      <div v-if="activate">
        <h3>Overview ({{ speedScalar }}x speed)</h3>
        <h5>{{ metric(sum, 2, 12, true) }} cookies produced <br>
        Avg. {{ metric(rate, 2, 9, true) }}/min ({{ Math.round(calcProdClick*(60/calcTime)/rate *1000)/10 || 0}}% manual)</h5>
        <ul style="list-style-type:none; padding-left: 0; font-size: 1.5rem;">
          <li><strong>Cookies per click:   </strong>{{ metric(dcClick, 2, 9, true) }}</li>
          <li><strong>Cookies per second: </strong>{{ metric(dcSec, 2, 9, true) }}</li>
          <li><strong>Playtime: </strong>{{ (playtime/60).toFixed(2) }} minutes</li>
        </ul>
      </div>
      <div v-else>
        <h3 style="font-size: 3rem;">
          <strong>SYSTEMS OFFLINE</strong>
        </h3>
      </div>

      <div style="">
        <div class="">
          <button class="unlocked debug-button" @click="dbDouble">
            <h4>debug: 2x cookie</h4>
          </button><br>
          <button class="unlocked debug-button" @click="lumen += 1000">
            <h4>debug: 1k lumen</h4>
          </button><br>
          <button class="unlocked debug-button" @click="addCookie(1e9); upgrades.start.upgradeFunc(); activate=true">
            <h4>debug: 1e9 cookies</h4>
          </button><br>
          <button class="unlocked debug-button" @click="speedScalar *= 2">
            <h4>debug: 2x game speed</h4>
          </button><br>
          <button class="unlocked debug-button" @click="speedScalar /= 2">
            <h4>debug: 0.5x game speed</h4>
          </button><br>
          <button class="unlocked debug-button" @click="showUps = true">
            <h4>debug: show every upgrade</h4>
          </button><br>
          <button class="unlocked debug-button" @click="addMsg(Date.now())">
            <h4>debug: add terminal msg</h4>
          </button><br>
          <button class="unlocked debug-button" @click="() => {activate=true; dcClick=dcClick ? 1 : dcClick;
            addMsg('Intro skipped.')}">
            <h4>debug: skip intro</h4>
          </button><br>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>

export default {
  name: 'App',
  data() {  
    document.title = `v0.jul22.2 spacey cookie clickey`
    function getImgUrl(pet) {
      var images = require.context('./assets/', false, /\.png$/)
      return images('./' + pet + ".png")
    }
    const sampling = 5
    const prefixes = ["", "k", "M", "B", "T", "Qd", "Qn", "Sxt", "Spt", "Oct", "Non", "Dec"]

    const clusters = ["Andromeda", "Antlia", "Apus", "Aquarius", "Aquila", "Ara", "Aries", "Auriga", "Australe",
            "Australis", "Austrinus", "Bootes", "Borealis", "Berenices", "Caelum", "Camelo", "Cancer",
            "Canis", "Capricornus", "Carina", "Cassiopeia", "Centaurus", "Cepheus", "Cetus", "Chamaeleon", 
            "Circinus", "Columba", "Coma", "Corona", "Corvus", "Crater", "Crux", "Cygnus", "Delphinus", 
            "Dorado", "Draco", "Equuleus", "Eridanus", "Fornax", "Gemini", "Grus", "Hercules", "Horologium", 
            "Hydra", "Hydrus", "Indus", "Lacerta", "Leo", "Lepus", "Libra", "Lupus", "Lynx", "Lyra", "Mensa", 
            "Microscopium", "Monoceros", "Musca", "Norma", "Octans", "Ophiuchus", "Orion", "Pavo", "Pegasus", 
            "Perseus", "Phoenix", "Pictor", "Pisces", "Pardalis", "Puppis", "Pyxis", "Reticulum", "Sagittarius", 
            "Scorpius", "Sculptor", "Scutum", "Serpens", "Sextans", "Taurus","Telescopium", "Triangulum", 
            "Tucana", "Ursa", "Vela", "Venatici", "Virgo", "Volans", "Vulpecula"]

    const greeks = ["Vega", "Alpha", "Beta", "Gamma", "Delta", "Epsilon", "Zeta", "Eta", "Theta", "Iota", "Kappa", 
                    "Lambda", "Mu", "Nu", "Omicron", "Pi", "Rho", "Sigma", "Tau", "Upsilon", "Phi", "Chi", "Omega"]

    const planet_pool = {
        star: {
          category: "Star",
          Star: {
            icon: "star",
            description: "star description",
          }
        },
        terra: {
          category: "Terrestial",
          list: ["Icy", "Rocky", "Metallic", "Oceanic", "Carbon", "Iron", "Ashen", 
                "Barren", "Lava", "Terranean", "Toxic", "Desert", "Protoplanet"],
          iconheader: "terra",
          Icy: {
            icon: "",
            description: "icy body description",
            color: "hsl(186, 69%, 44%)",
          },

          Rocky: {
            icon: "",
            description: "rocky body description",
            color: "hsl(37, 100%, 41%)",
          },

          Metallic: {
            icon: "",
            description: "metallic body description",
            color: "hsl(201, 9%, 49%)",
          },

          Oceanic: {
            icon: "",
            description: "ocean body description",
            color: "hsl(190, 100%, 39%)",
          },         

          Carbon: {
            icon: "",
            description: "carbon body description",
            color: "hsl(186, 69%, 44%)",            
          },

          Iron: {
            icon: "",
            description: "iron body description",
            color: "hsl(27, 100%, 37%)",
          },

          Ashen: {
            icon: "",
            description: "ashen body description",
            color: "hsl(27, 30%, 35%)",               
          },

          Barren: {
            icon: "",
            description: "barren body description",
            color: "hsl(19, 11%, 40%)",
          },

          Lava: {
            icon: "",
            description: "lava body description",
            color: "hsl(25, 100%, 43%)",
          },

          Terranean: {
            icon: ["terra"],
            description: "terranean body description",
            color: "hsl(150, 100%, 39%)",
          },

          Toxic: {
            icon: "",
            description: "toxic body description",
            color: "hsl(103, 71%, 38%)" ,
          },

          Desert: {
            icon: "",
            description: "desert body description",
            color: "hsl(55, 40%, 55%)",
          },

          Protoplanet: {
            icon: "",
            description: "protplanet description",
            color: "hsl(272, 49%, 30%)",
          },

        },
        giant: {
          category: "Giant",
          list: ["Ammonia", "Water", "Methane", "Hydrogen", "Helium", "Ice", "Chthonian", "Cold_Gas", "Hot_Gas"],
          iconheader: "giant",
          Ammonia: {
            icon: "",
            description: "ammonia description",
            color: "hsl(151, 45%, 32%)",
          },

          Water: {
            icon: "",
            description: "water giant description",
            color: "hsl(218, 61%, 33%)",
          },

          Methane: {
            icon: "",
            description: "methane giant description",
            color: "hsl(188, 20%, 48%)",
          },

          Hydrogen: {
            icon: "",
            description: "hydrogen giant description",
            color: "hsl(62, 10%, 51%)",
          },

          Helium: {
            icon: "",
            description: "helium giant description",
            color: "hsl(316, 17%, 55%)",
          },

          Ice: {
            icon: ["giant2", "giant4"],
            description: "ice giant description",
            color: "hsl(214, 28%, 48%)",
          },

          Chthonian: {
            icon: "",
            description: "chthonian giant description",
            color: "hsl(9, 77%, 38%)",
          },

          Cold_Gas: {
            icon: "",
            description: "cold gas description",
            color: "hsl(190, 33%, 52%)",
          },

          Hot_Gas: {
            icon: "" ,
            description: "hot gas description",
            color: "hsl(344, 79%, 43%)",
          },
        },
        special: {
          category: "",
          list: ["Lumenyte_Cluster", "Gaia_Terranean", "Derelict", "Lagrange_Nebula", "Ancient_Vessel",
                "Virtual_Nexus", "Artificial_Protostar", "Psionspatial_Anomaly"],
          // hyperfolded spaces has been temporarily disabled
          iconheader: "special",

          Lumenyte_Cluster: {
            icon: ["special1"],
            description: "buncha lumenyte"
          },

          Psionspatial_Anomaly: {
            icon: ["special1"],
            description: "wormhole stuff",
          },

          Lagrange_Nebula: {
            icon: ["special2"],
            description: "lagrange nebula description",
          },

          Ancient_Vessel: {
            icon: ["special3"],
            description: "alien stuff",
          },

          Gaia_Terranean: {
            icon: ["special4"],
            description: "earthlike with advanced lifeform",
          },

          Derelict: {
            icon: ["special5"],
            description: "more alien stuff",
          },

          Virtual_Nexus: {
            icon: ["special6"],
            description: "alien megabase",
          },

          Hyperfolded_Psionspaces: {
            icon: ["special7"],
            description: "non euclid space, buncha planets in one"
          },

          Artificial_Protostar: {
            icon: ["star"],
            description: "a star but artificial",
          },
        },
      }

    window.setInterval(this?.autoCookie, 1000/24);
    window.setInterval(() => {
      this.dcClick *= (1 + this.d2cClick/1000)**(this.speedScalar);
      this.dcSec *= (1 + this.d2cSec/1000)**(this.speedScalar);
    }, 1000)
    window.setInterval(this.calcProd, sampling*1000);


    function generateSystem(count = 10, p_special = 0.0375, cstlltn = '') {
      let constellation = cstlltn ? cstlltn : choose(clusters)
      let system_name = choose(greeks)
      let bodies = []

      // generate star here
      bodies.push({
        name: "Star", number: 0, category: "star",
        dispname: "Star",
        icon: "star", banner: "special-banner-red"
      })

      let planet_count = Math.ceil(Math.sqrt(Math.random()) * count + 1)

      // iteratively add more planets
      for (let i = 0; i < planet_count; i++) {
        let rng_category = Math.random()
        var p_giant = Math.min(0.7, Math.sqrt(i)/(planet_count*0.8) + 0.1)
        var genp = {
          name: '',
          category: '',
          number: i + 1,
          icon: '',
          banner: '',
          surface: {},
        }

        if (rng_category <= p_special) {
          genp.name = choose(planet_pool.special.list)
          genp.category = "special"
          genp.icon = planet_pool.special[genp.name].icon
          genp.banner = 'special-banner-purple'
        }
        else {
          if (rng_category <= p_special + p_giant) {
            genp.name = choose(planet_pool.giant.list)
            genp.category = "giant"
          }

          else {
            genp.name = choose(planet_pool.terra.list)
            genp.category = "terra"
          } 

          let poolicon = planet_pool[genp.category][genp.name].icon
          genp.icon = poolicon ? choose(poolicon) : 
          genp.category + Math.ceil(Math.random() * 5)
          genp.banner = genp.name
        }
        genp.dispname = genp.name.replace("_", " ") + ' ' + planet_pool[genp.category].category
        bodies.push(genp)
      }

      console.log(bodies)
      return {
        cluster: constellation,
        star: system_name,
        system: system_name + "_" + constellation,
        name: `${system_name} ${constellation}`,
        bodies: bodies,
      }

    }

    function formatNum(x, e=12, d=6, r=2) {
        return x > 10**e ? x.toExponential(d) :
        x.toLocaleString(undefined, {minimumFractionDigits: r,maximumFractionDigits: r})
    } 

    function metric(x, dec=2, threshold=5, local=true, ldec=2) {
      if (x >= 10**threshold && x < 1e36) {
        let base = Math.floor(Math.log10(x)/3)  
        return `${(x/1000**base).toFixed(dec)}${prefixes[base]}`}
      return local ? formatNum(x, 12, 6, ldec) : x
    }

    function formatDec(x, e=2) {
      return (x < 10**(e-1) ? '0'.repeat(e-1) : '') + x
    }

    function levelName(lvl) {
      let a = 132129363.618
      let b = -132129364.94
      let s = 1.091880684e-8
      let k = 5
      // i assure you these numbers make sense and are chosen methodically
      return Math.floor(a * Math.max(lvl, k)**s + b)
    }

    function computeCostDecay(exf, eta) {
      return (exf - 1) / eta + 1
    }

    function choose(choices) {
      var index = Math.floor(Math.random() * choices.length);
      return choices[index];
    }

    function scrollToBottom (id) {
       var div = document.getElementById(id);
       div.scrollTop = div.scrollHeight - div.clientHeight;
    }

    return {
      getImgUrl,
      
      formatNum,
      formatDec,
      metric,
      levelName,
      choose,

      clusters,
      greeks,
      planet_pool,
    
      scrollToBottom,
      name: "",
      terminalInputContent: "",
      introSpeed: 1/12,

      activate: false,
      dispmap: false,
      dispclosing: false,
      dispinfo: {},

      cookie: 0,
      sum: 0,
      lumen: 0,
      speedScalar: 8,
      playtime: 0,

      exfCost: 1.7,
      exfValue: 1.4,
      costDecay: 1.0028,
      rate: 0,

      travelCost: 2000,
      travelTime: 40,
      exfDistance: 1.26,
      exfDistanceCost: 1.82,
      isTraveling: false,

      calcTime: sampling, 
      calcProdTmp: 0,
      calcProdClick: 0,
      calcProdManual: 0,

      showUps: false,

      dcClick: 0,
      dcSec: 0,
      d2cClick: 0,
      d2cSec: 0,

      dlumDrone: 0.46,
      dlumDroneSigma: 0.3,
      lumenDroneActive: false,

      star_systems: {},
      loc_system: {name:'Establishing system map..'},
      loc_planet: {dispname:'Establishing system map..', icon:"empty", surface:{}},

      terminal: ["# 8x speed enabled by default for playtesting",
                "# Fast logs mode is enabled, messages will be sped up",
                "# Game balance has been defenestrated outta window in favour of adding features",
                "# Use debug buttons on the lower left side",
                "",
                "(!!) Catastrophic system-wide error."],

      lorem: "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse molestie, nisl ac interdum finibus, nibh ante fermentum lorem, et mattis erat metus id diam. Phasellus ac ex varius leo mattis molestie. Proin aliquet nec arcu in ullamcorper. Etiam feugiat tellus non augue tempus lacinia. Proin pellentesque rhoncus massa vel fermentum. Pellentesque lacinia magna blandit tellus placerat consequat. Morbi ac sollicitudin nisl. Integer laoreet mollis dapibus. Aliquam molestie sem id lacus aliquam, quis pretium quam condimentum. Sed in magna elit. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Maecenas vitae aliquam libero. Nulla feugiat, elit ut ultricies eleifend, leo nibh molestie elit, ac mollis urna dolor non ipsum.",


      upgrades: {
        /*
        empty: {
          name: "empty",
          upgradedNames: ["",],
          description: "empty",
          level: 0,
          unlock: 0,
          cost: 0,
          value: 0,
          max: 99,
          exfUpCost: 1.,
          exfUpValue: 1.,
          upgradeFunc: () => {
            let upg = this.upgrades.empty;
            if (this.cookie >= upg.cost && upg.level < upg.max) {
              ""
            }
          },
        },
        */

        // god is dead
        start: {
          name: "Restore Systems",
          upgradedNames: ["Restore Systems", "Systems Online"],
          description: "",
          level: 0,
          unlock: 0,
          cost: 0,
          max: 1,
          upgradeFunc: () => {
            let upg = this.upgrades.start;
            if (this.cookie >= upg.cost && upg.level < upg.max) {
              upg.level = 1
              this.addMsg("Attempting system restoration")

              var msgs = [
                "Troubleshooting modules..",
                "Attempting self-repair..",
                "(!) Repair failed.",
                "Executing shutdown and reboot..",
                "Power generator online..",
                "Life support online..",
                "Onboard computer online..",
                "Cookie replicators online..",
                "Cookie foundry online..",
                "Finalizing..",
                "",
              ]
              var delays = [500]

              for (let i = 0; i < msgs.length; i++) {
                delays.push(delays[i] + (Math.random() * 2000) + 300 )
              }

              for (let j = 0; j < msgs.length; j++) {
                setTimeout(() => {
                  this.addMsg(msgs[j])
                }, delays[j] * this.introSpeed)
              }

              setTimeout(() => {
                this.dcClick = 1
                this.activate = true;
                upg.name = upg.upgradedNames[upg.level]
                this.addMsg("Systems restored successfully")
              }, (delays.slice(-1)[0] + 3000) * this.introSpeed)

              setTimeout(() => {
                this.addMsg("The following modules are heavily damaged and failed to reboot: \
                  <Navigations>; <Propulsions>; <Communications>; UNKNOWN; UNKNOWN; UNKNOWN; (!)ERROR: DATA CORRUPTION; UNKNOWN")
              }, (delays.slice(-1)[0] + 4600) * this.introSpeed)

              setTimeout(() => {
                this.addMsg("Cookie replicator fully functional")
              }, (delays.slice(-1)[0] + 6000) * this.introSpeed)

            } 
          },
        },
        mining: {
          name: "Cookie Mining",
          upgradedNames: ["Cookie Mining",
                    "Cookie Asteroids Extraction",
                    "Exostellar Cookie Scoops", 
                    "Non-Baryonic Cookie Metallurgy",
                    "Intergalatic Hypercookie Cultivation",
                    "Abstract Temporal Cookie Sinks"],
          description: "Upgrades cookies per click",
          level: 0,
          unlock: 10,
          cost: 60,
          value: 0.7,
          max: 99,
          exfUpCost: 1,
          exfUpValue: 1.04,
          upgradeFunc: () => { 
            let upg = this.upgrades.mining;
            if (this.cookie >= upg.cost && upg.level < upg.max) {
              this.dcClick += upg.value
              this.cookie -= upg.cost
              upg.cost *= this.exfCost * upg.exfUpCost
              upg.value *= this.exfValue * upg.exfUpValue
              upg.level++
              upg.name = upg.upgradedNames[this.levelName(upg.level)]
              this.exfCost = computeCostDecay(this.exfCost, this.costDecay)
            }
          },
        },
        manufacturing: {
          name: "Cookie-Based Manufacturing",
          upgradedNames: ["Cookie-Based Manufacturing", 
                          "Advanced Cookie Replication",
                          "Exotic Cookie Xenoindustries",
                          "Supralight Cookie Hyperfolding",
                          "Hyperium-Powered Manufacturing",
                          "N-Dimensional Hypercookie Bakeries"],
          description: "Upgrades cookies per second",
          level: 0,
          unlock: 150,
          cost: 105,
          value: 0.92,
          max: 99,
          exfUpCost: 1,
          exfUpValue: 1.11,
          upgradeFunc: () => {
            let upg = this.upgrades.manufacturing
            if (this.cookie >= upg.cost && upg.level < upg.max) {
              this.dcSec += upg.value
              this.cookie -= upg.cost
              upg.cost *= this.exfCost * upg.exfUpCost
              upg.value *= this.exfValue * upg.exfUpValue
              upg.level++
              upg.name = upg.upgradedNames[this.levelName(upg.level)]
              this.exfCost = computeCostDecay(this.exfCost, this.costDecay)
            }
          },
        },
        extraction: {
          name: "Advanced Refineries",
          upgradedNames: ["Advanced Refineries",
                          "Low-Kelvin Cookie Enrichment",
                          "Hyperlumenyte Cookie Catalyzers",
                          "Graviton-Based Ore Filtration",
                          "Exa-Volt Subatomic Manipulation",
                          "Quantum Exomatter Engineering"],
          description: "Improves Cookie Mining effectiveness",
          level: 0,
          unlock: 3000,
          cost: 4700,
          value: 1.006,
          max: 50,
          exfUpCost: 3.89,
          upgradeFunc: () => {
            let upg = this.upgrades.extraction;
            if (this.cookie >= upg.cost && upg.level < upg.max) {
              let rho = this.exfValue * this.upgrades.mining.exfUpValue
              let alpha = this.upgrades.mining.value / 
              rho**(this.upgrades.mining.level)

              rho *= upg.value
              //geometric series
              this.dcClick += alpha*(1-rho**(this.upgrades.mining.level+1))/(1-rho)
              this.upgrades.mining.exfUpValue *= upg.value
              this.cookie -= upg.cost
              this.d2cClick += (upg.value - 1)
              upg.cost *= this.exfCost * upg.exfUpCost
              upg.level++
              upg.description = `Improves ${this.upgrades.mining.name} effectiveness`
              upg.name = upg.upgradedNames[this.levelName(upg.level*2)]
              this.exfCost = computeCostDecay(this.exfCost, this.costDecay)
            }
          },
        },
        ai_automation: {
          name: "Manufacturing Robotics",
          upgradedNames: ["Manufacturing Robotics",
                          "Adaptive Autonomous Industries",
                          "Wave Function Encoding",
                          "Infinite Variable Computing",
                          "Hypertopological Supercomputers",
                          "Intertemporal Sub-Planck Networks"],
          description: "Improves Cookie-Based Manufacturing effectiveness",
          level: 0,
          unlock: 50000,
          cost: 41500,
          value: 1.0083,
          max: 50,
          exfUpCost: 3.81,
          upgradeFunc: () => {
            let upg = this.upgrades.ai_automation;
            if (this.cookie >= upg.cost && upg.level < upg.max) {
              let rho = this.exfValue * this.upgrades.manufacturing.exfUpValue
              let alpha = this.upgrades.manufacturing.value / 
              rho**(this.upgrades.manufacturing.level)

              rho *= upg.value
              //geometric series
              this.dcSec += alpha*(1-rho**(this.upgrades.manufacturing.level+1))/(1-rho)
              this.upgrades.manufacturing.exfUpValue *= upg.value
              this.cookie -= upg.cost    
              this.d2cSec += (upg.value - 1)
              upg.cost *= this.exfCost * upg.exfUpCost
              upg.level++
              upg.description = `Improves ${this.upgrades.manufacturing.name} effectiveness`
              upg.name = upg.upgradedNames[this.levelName(upg.level*2)]
              this.exfCost = computeCostDecay(this.exfCost, this.costDecay)
            }
          },
        },            
        // god remains dead
      },

      mods: {
        /*
        empty: {
          show: true,
          name: "",
          upgradedNames: "",
          description: "",
          level: 0,
          unlock: 0,
          cost_cookie: 0,
          cost_lumen: 0,
          max: 0,
          upgradeFunc: () => {
            let mod = this.mods.empty
            if (this.cookie >= mod.cost_cookie && this.lumen >= mod.cost_lumen
                && mod.level < mod.max) {

            }
          }
        },
        */

        navigation: {
          show: true,
          name: "Navigation System",
          upgradedNames: "",
          description: "Enables intrasystem navigation, stellar identification",
          level: 0,
          unlock: 1000,
          cost_cookie: 650,
          cost_lumen: 0,
          max: 1,
          upgradeFunc: () => {
            let mod = this.mods.navigation
            if (this.cookie >= mod.cost_cookie && this.lumen >= mod.cost_lumen
                && mod.level < mod.max) {
              mod.name = "Interstellar Navigation Systems"
              mod.description = "Allows for interstellar star charting, reveals nearby stellar bodies"
              mod.cost_cookie = 1400000
              mod.cost_lumen = 175.27
              mod.level++

              this.addMsg("Surveying system orbital plane..")
              setTimeout(() => {
                let gen = generateSystem(400, 0.05, '')
                this.star_systems[gen.system] = gen
                this.loc_system = this.star_systems[gen.system]
                this.loc_planet = choose(this.loc_system.bodies.slice(1))

                this.addMsg(`System mapped, ${gen.bodies.length} astronomical bodies found`)
                for (let i = 0; i < gen.bodies.length; i++) {
                  if (gen.bodies[i].category == "special") {
                    this.addMsg("Unknown anomaly detected on body #" + i)
                  }
                }

                setTimeout(() => {
                  this.addMsg("Planetary surface scan required for further information")
                }, 2000 * this.introSpeed)
                setTimeout(() => {
                  this.addMsg("Currently on orbit around " + this.loc_system.name + ' ' + this.loc_planet.number )
                }, 2400*this.introSpeed)
              }, 4000*this.introSpeed)

            }
          }
        },

        surface: {
          show: true,
          name: "Planetary Surface Survey",
          upgradedNames: "",
          description: "Launches reconnaissance probes to survey planetary surfaces",
          level: 0,
          unlock: 17000,
          cost_cookie: 10000,
          cost_lumen: 0,
          max: 1,
          upgradeFunc: () => {
            console.log("surface upgrade")
            let mod = this.mods.surface
            if (this.cookie >= mod.cost_cookie && this.lumen >= mod.cost_lumen
                && mod.level < mod.max && this.loc_planet.name && !this.loc_planet.surface.scanned) {
              mod.level++
              this.addMsg("Surface reconnaissance probes deployed")
              this.loc_planet["surface"] = {scanned: false}

              setTimeout(() => {
                this.addMsg("Lumenyte deposits found on surface layer")
                this.loc_planet.surface["scanned"] = true
                this.loc_planet.surface["lumen_deposit"] = Math.random() * 2 + 1.96
              }, 5000 * this.introSpeed)
            }
          }
        },

        propulsion: {
          show: true,
          name: "Subluminal Propulsion Systems",
          upgradedNames: "",
          description: "Allows traversal within intrasystem space",
          level: 0,
          unlock: 60000,
          cost_cookie: 42700,
          cost_lumen: 1.45,
          max: 1,
          upgradeFunc: () => {
            let mod = this.mods.propulsion
            if (this.cookie >= mod.cost_cookie && this.lumen >= mod.cost_lumen
                && mod.level < mod.max) {
                mod.level++
                mod.name = "Psionic Wave Function Manipulator"
                mod.description = "Enables traversal within Psion-Space, allowing supraluminal tunneling to other star systems"
                mod.cost_cookie = 5.37e6
                mod.cost_lumen = 645
            }   
          }
        },        

        lumen_extract: {
          show: true,
          name: "Advanced Lumenyte Extraction",
          upgradedNames: "",
          description: "Greatly improves lumenyte extraction technology",
          level: 0,
          unlock: 375000,
          cost_cookie: 284700,
          cost_lumen: 18.6,
          max: 1,
          upgradeFunc: () => {
            let mod = this.mods.lumen_extract
            if (this.cookie >= mod.cost_cookie && this.lumen >= mod.cost_lumen
                && mod.level < mod.max) {
                ""
            }   
          }
        },
      },
     // what lies after..
    }
  },

  methods: {
   addCookie(x) {
      x *= this.speedScalar
      this.cookie += x
      this.sum += x
    },

    autoCookie() {
      this.addCookie(this.dcSec/24)
      this.playtime += this.speedScalar/24
    },

    clickCookie() {
      this.addCookie(this.dcClick)
      this.calcProdManual += this.dcClick * this.speedScalar
    },

    calcProd() {
      this.rate = (this.sum - this.calcProdTmp) * (60/this.calcTime)
      this.calcProdTmp = this.sum
      this.calcProdClick = this.calcProdManual
      this.calcProdManual = 0
    },

    calcDistanceCost(x, y) {
      return this.travelCost * Math.abs(this.exfDistanceCost**x - this.exfDistanceCost**y)
    },

    calcTimeCost(x, y) {
      return this.travelTime * Math.abs(this.exfDistance**x - this.exfDistance**y)
    },

    dbDouble() {
      this.cookie *= 2
      this.sum += this.cookie
    },
    db10() {
      this.cookie *= 10
      this.sum += this.cookie * 9
    },

    addMsg(s) {
      this.terminal.push(s)
      this.scrollToBottom("terminal-div")
    },

    intrasystemTravel(end) {
      const start = this.loc_planet
      this.isTraveling = true
      this.star_systems[this.loc_system.system].bodies[this.loc_planet.number] = this.loc_planet
      this.loc_planet = {name: "space", dispname: "Traversing space..", icon: "space", surface: {}}
      this.cookie -= this.calcDistanceCost(start.number, end.number)

      let tt = this.calcTimeCost(start.number, end.number) * (Math.random()/5 + 0.9)
      console.log(tt/this.speedScalar)
      this.addMsg("Engines engaged, estimated time to arrival: " + this.metric(tt, 2,4,1,2) + ' seconds')
      window.setTimeout(() => {
        this.addMsg("Arrived at destination")
        this.loc_planet = end
        this.mods.surface.level = 0
        this.isTraveling = false
      }, 1000*tt/this.speedScalar)
    },

    upgradeClick(x, c) {
      this.dcClick += x
      this.cookie -= c
    },

    lumenMining() {
      this.lumenDroneActive = true;
      setTimeout(() => {
        let x = Math.min(this.dlumDrone + Math.random() * this.dlumDroneSigma, this.loc_planet.surface.lumen_deposit)
        this.lumen += x
        this.lumenDroneActive = false
        this.addMsg(`Extraction operations complete, ${this.metric(x)} lumen refined`)
        this.loc_planet.surface.lumen_deposit -= x
      }, (Math.random() * 40000 + 36000)/this.speedScalar)
    },

    closeMap() {
      this.dispclosing = true
      setTimeout(() => {
        this.dispmap = false
        this.dispclosing = false 
        this.dispinfo = {}
      }, 400)
    },

    logUpgrades() {
      console.log(this.upgrades)
    },

    bruh() {
      console.log("yup")
      return "yep"
    },
  }
}
// what awaits ahead..
</script>

<style>
  #app {
    font-family: Cairo, Helvetica, sans-serif;
    
  }
  html,
  body{
    line-height: 1.2;
    margin: 0; height: 100%; overflow: scroll;
    background-color: none;
    color: white;
    font-size: 1.425vmin;

  }
  ::-webkit-scrollbar {
    width: 0;
    height: 0;
    background: transparent;

  }



  ul {
    margin: 0.2rem;

  }
  h1,h2,h3,h4,h5 {
    margin-top: 0.1rem;
    margin-bottom: 0.1rem;
    font-weight: normal;
  }

  textarea, input {
    outline: none;
    border: none;
    overflow-wrap: break-word;
  }

  /*general buttoning needs*/

  button {
    font-family: Cairo, sans-serif;
    line-height: 1.5;
  }

  .lumen-text {
    text-align: center;
    color: #00fff2;
    text-shadow: rgba(0,255,242,0.7) 0px 0px 0.3rem, 
                rgba(0,255,242) 0px 0px 0.9rem, 
                rgba(0,255,242) 0px 0px 1.3rem,
                rgba(0,255,242,0.5) 0px 0px 1.8rem;
  }

  .vert-line {
    width: 2px;
    background-color: white;
    height: 2vw;
  }

  @keyframes activation {
    0% {background: none;}
    20% {background: hsl(0, 0%, 70%);}
    100% {background: none;}
  }

  @keyframes scaling-up {
    0% {transform: scale(1)}
    100% {transform: scale(1.04);}
  }

  @keyframes scaling-down {
    0% {transform: scale(1.04)}
    100% {transform: scale(0.95);}
  }

  .center {
    display: grid;
    place-items: center;
  }

  .scrollbar-div {
    overflow-y: scroll;  
  }

  .unlocked { 
    transition: all .2s ease-in-out; }

  .unlocked:hover { 
    transform: scale(1.035); }

  .unlocked:active {
    transform: scale(0.95); 
    opacity: 90%;
    transition: all .1s ease-in-out;
   }

  .upgrade-button:active {
    background: hsl(0, 0%, 30%);
    transition: all .1s ease-in-out;
  }

  .locked {
    opacity: 45%;
    transform: scale(0.95);
    transition: all .2s ease-in-out; 
  }

  .cookielocked {
    opacity: 32%;
    transform: scale(0.7);
    transition: scale .4s ease-in-out;  
  }

  .cookie-button {
    transform: scale(1.05);
    user-select: none;
  }

  .planet-button {
    user-select: none;
  }

  .debug-button {
    font-size: 1rem;
    width: 16rem;
    height: 1.6rem;
  }

  .terminal-text {
    font-family: JetBrains Mono;
    font-size: 1.15rem;
    padding: 0.1rem;
    margin-left: 0.5rem;

  }

  @keyframes msg-flash {
    0% {
      background: hsl(0, 0%, 80%);
    }
    100% {
      background: transparent;
    }
  }

  @keyframes blink-caret {
    from, to { border-color: transparent }
    50% { border-color: white; }
  }

  @keyframes blinking {
    from, to { opacity: 60% }
    50% { opacity: 100% }
  }

  .notice-blinking {
    animation: blinking .85s infinite;
  }

  .terminal-msg {
    margin-bottom: 0.35rem;
    line-height: 0.9em;
    animation: 
      msg-flash .7s
  }

  .typewriter {
    overflow: hidden;
    border-right: .15em solid orange;
    width: 5%;
    animation: 
      blink-caret .75s step-end infinite;
  }

  .console-input {
    font-family: JetBrains Mono;
    color: white;
    font-size: 1.15rem;
    margin-left: 0.4rem;

    background:transparent;
    width:22vw;
  }

  /*main grid*/

  .grid-container {
    display: grid;
    grid-template-areas:
      'menu   header  header  header  upper-right'
      'menu   main    main    main    upper-right'
      'menu   main    main    main    upper-right'
      'menu   main    main    main    upper-right'
      'misc   main    main    main    lower-right'
      'misc   footer  footer  footer  lower-right';
        grid-gap: 2px;
    padding: 0px;
    width: 100vw;
    height: 100vh;
  }
  .grid-container > div {
    background-color: rgba(14, 10, 20, 1);
    text-align: center;
    font-size: 1.6em;  
    place-items: center;
  }
  .item1 { 
    width: 42vw;
    height: 15vh;
    grid-area: header;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .item2 {
    width: 33vw;
    height: 60vh;
    grid-area: menu; 
  }
  .item3 { 
    width: 42vw;
    height: 61vh;
    grid-area: main; 
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .item4 { 
    width: 25vw;
    height: 60vh;
    grid-area: upper-right; 
  }
  .item5 { 
    width: 25vw;
    height: 40vh;
    grid-area: lower-right;
   }

  .item6 {
    width: 42vw;
    height: 24vh;
    grid-area: footer;

  }

  .item7 {
    width:  33vw;
    hegiht: 40vh;
    grid-area: misc;
  }

  /*upgrade button*/

  .upgrade-grid {
    display: grid;
    grid-template-areas: 
    'upper upper upper upper upper upper lv'
    'lower lower lower lower lower lower lv';
    grid-gap: 0px;
    padding: 0;
  }

  .grid-button-upper {
    padding-top: 0.5rem;
    line-height: 1.15;
    overflow: hidden;
    grid-area: upper;
    margin: 0;
  }

  .grid-button-lower {
    line-height: 1.15;
    overflow: hidden;
    grid-area: lower;
    margin: 0;
  }

  .grid-button-right {
    overflow: hidden;
    grid-area: lv;
    display: flex;
    flex-direction: column;
    text-align: center;
    justify-content: center;
    align-items: center;
    padding-left: 0.7rem;
  }

  .upgrade-button {
    width: 32vw;
    color: white;
    background: none;
    border:  0.22rem solid white;
    border-radius: 1rem;
    margin-bottom: 0.2rem;
    animation: activation 0.4s;
  }

  .desc-header {
    color: white;
    background: none;
    border:  0.22rem solid white;
    border-radius: 1rem;
    margin-bottom: 0.2rem;
  }

  .module-button {
    width: 23.5vw;
    margin-bottom: 0.2rem;
    padding-bottom: 0.6rem;
  }

  .map-button {
    width: 6vw;
    padding: 0.4rem;
    border:  3px solid white;
    border-radius: 1rem;
    place-self: center;
  }

  /* navigation grid */

  .nav-grid {
    display: grid;
    grid-template-areas: 
    'nav-up nav-up nav-up nav-up nav-icon'
    'nav-mid nav-mid nav-mid nav-mid nav-icon'
    'nav-low nav-low nav-low nav-low nav-icon2';
    grid-gap: 0px;
    animation: nav-activation 0.4s;
  }

  .nav-upper {
    place-self: center;

    width: 30vw;
    height: 12vh;
    border: 0px dashed white;
    font-size: 1.75rem;
    grid-area:  nav-up;
    display: flex;
    align-items: center;
  } 

  .nav-middle {
    width: 30vw;
    height: 6vh;
    border: 0px dashed white;
    grid-area:  nav-mid;
    padding-left: 2rem;
    padding-right: 1rem;
    font-size: 1.4rem;
    display: grid;
    place-items: center;
  }

  .nav-lower {
    width: 30vw;
    height: 5vh;  
    border: 0px dashed white;
    grid-area:  nav-low;
    padding-left: 2rem;
    padding-right: 1rem;
    display: grid;
  }

  .nav-disp {
    width: 8vw;
    height: 18vh;
    border: 0px dashed white;
    padding: 0;
    grid-area: nav-icon;
    display: grid;
    place-self: center;
    place-items: center;
  }

  .nav-bar {
    width: 8vw;
    height: 5vh;
    border: 0px dashed white;
    grid-area: nav-icon2;
    display: grid;
    place-self: center;
  }

  .nav-mid-div {
    display: flex;
    flex-direction: rows;
    align-items: center;
    justify-content: space-between;
  }

  @keyframes map-open {
    0% {
      transform: translateY(0);}

    100% {
      transform: translateY(-85.5vh);}
  }

    @keyframes map-close {
    0% {
      transform: translateY(-85.5vh);}

    100% {
      transform: translateY(0);}
  }

  .map {
    width: 42vw;
    height: 85vh;  
    border:  3px solid white;
    border-radius: 1rem;
    grid-area: footer;
    left: calc(33vw - 1px);
    top: 100vh;
    position: absolute;
    animation: map-open .4s ease forwards;

    display: grid;
    grid-template-areas: 
    'map-header map-header map-header'
    'map-left   map-left   map-right'
    'map-left   map-left   map-right';
    grid-gap: 0px;
    padding: 0;
  }

  .map-header {
    height: 9vh;
    width: 42vw;
    grid-area: map-header;
    border-bottom: 1px solid white;

    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .map-left {
    height: 76vh;
    width: 24vw;
    grid-area: map-left;
    border-right: 1px solid white;
  }

  .map-right {
    height: calc(76vh - 2rem);
    width: calc(18vw - 2rem);
    grid-area: map-right;
    text-align: left;
  }

  .planet-div {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-left: 1vw;
    margin-right: 0.5vw;
  }

  .planet-button {
    border: none;
    background: none;
  }

  .planet-button:hover {
    transform: scale(1.2);
  }

  @keyframes desc-wrapper-open {
    0% {width: 4vw; 
      transform: translateX(6vw);
    }
    50% {width: 4vw; 
      transform: translateX(6vw);
    }
    100% {width: 16vw; 
      transform: translateX(0);
    }
  }

  .desc-anim1 {
    animation: desc-wrapper-open .55s ease;
  }

  @keyframes desc-slide {
    0% {transform: translateX(-6vw); opacity: 0%;}
    10% {opacity: 0%;}
    70% {opacity: 50%;}
    100% {transform: translateX(0); opacity: 100%;}
  }
  .desc-slide {
    animation: desc-slide 0.2s ease forwards;
  }

</style>

