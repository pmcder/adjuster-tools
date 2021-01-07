<template>
  <div class="widget" id="negotiation">
    <section class="wide">
      <h1>Negotiation Visualizer</h1>
      <a>
        This tool allows you to visualize your negotiations through each cycle
        of offer and demand. Enter your starting offer, your limit (the max of
        your negotiation range), and the claimant's demand to get started. Your
        limit will be marked on the graph and you will then be prompted to enter
        each offer and demand and will see a graph of each moving from their
        starting point toward each other. 
      </a>
      <br /><br />
      <b>Your starting offer amount : ${{ yourStart }}</b>
      <div class="bar">
        <div v-bind:style="userBarStyle"></div>
      </div>
      <div class="bar" id="highBar">
        <span id="limitBox" v-bind:style="styleObject"
          ><b>{{ yourLimit }}</b></span
        >
      </div>
      <div class="bar" id="claimantBarBackground">
        <div v-bind:style="claimantBarStyle"></div>
      </div>
      <div id="claimant">
        <b>Claimant initial demand: ${{ demands[0] }}</b>
      </div>
      <p>
        <label>Your Starting offer</label
        ><input type="number" v-model="yourStart" />
      </p>
      <p>
        <label>Your Limit</label><input type="number" v-model="yourLimit" />
      </p>
      <p>
        <label>Claimant demand</label><input type="number" v-model="demand" />
      </p>
        <p v-if="error" class="errorArea">
          <ul>
      <li v-for="error in errors" :key="error">{{ error }}</li>
    </ul>
      </p>
      Press submit to enter these values and start negotiating !
      <button v-on:click="setLimit">submit</button>
      </section>
       
      <div v-if="completed">
        The max of your range is {{ limitPercent.toFixed(2) }}% of the
        claimant's initial demand.
        <div>
          <table>
            <thead>
              <tr>
                <th colspan="2">negotiation overview</th>
              </tr>
              <tr>
                <td>offer</td>
                <td>demand</td>
              </tr>
            </thead>
            <tbody v-for="(off, index) in offers" :key="index">
              <tr>
                <td>${{ offers[index] }}</td>
                <td>${{ demands[index] }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        </div>
          <div v-if="completed" class="right">
          <p>enter your offer: <input type="number" v-model="offer" />
          <button v-on:click="addOffer">add offer</button></p>
          <p class="warningArea" v-if="offer > yourLimit">
            you exceeded your max but you can continue
          </p>
          <p>enter demand: <input type="number" v-model="demand" />
          <button v-on:click="addDemand">add demand</button></p>
        </div>
      </div>
    
  
</template>
    <style scoped>
#negotiation {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}

section.wide {
  grid-column-start: 1;
  grid-column-end: 3;
}

.right {
  grid-column-start: 2;
}
.warningArea {
  background-color: yellow;
  color: black;
}
#limitBox {
  border-left: solid black 10px;
}
#highBar {
  height: 20px;
}
table {
  color: #2c3e50;
}

thead {
  text-align: center;
}

tr {
  background-color: #8ecae6;
}

td {
  margin: 10px;
  padding: 20px;
}

div.bar {
  width: 100%;
  height: 50px;
  border: solid black 1px;
  background-color: white;
}
#claimantBarBackground {
  background-color: red;
}
#claimant {
  text-align: right;
}
</style>
    
    <script>
export default {
  name: "negotiate",

  data() {
    return {
      title: "vue title",
      yourStart: null,
      yourLimit: null,
      demand: null,
      limitPercent: null,
      completed: false,
      offer: null,
      error: false,
      styleObject: {
        color: "red",
        position: "relative",
        left: "0",
      },
      userBarStyle: {
        height: "50px",
        width: "0%",
        background: "green",
      },
      claimantBarStyle: {
        height: "50px",
        width: "100%",
        position: "relative",
        left: "0",
        background: "white",
      },
      offers: [],
      demands: [],
      errors: [],
    };
  },

  methods: {
    setLimit() {
      this.error = false;
      this.errors = [];
      if (!this.validateLimit()) {
        this.error = true;
        return false;
      }
      this.limitPercent = parseFloat(this.yourLimit / this.demand) * 100;
      this.styleObject.left = 0;
      this.styleObject.left =
        parseInt(this.styleObject.left) + this.limitPercent + "%";
      this.offers.push(this.yourStart);
      this.demands.push(this.demand);
      console.log(this.offer);
      console.log(this.demands[0]);
      this.userBarStyle.width =
        parseFloat(this.yourStart / this.demands[0]) * 100 + "%";
      this.completed = true;
    },
    validateLimit() {
      this.limitPercent = parseFloat(this.yourLimit / this.demand) * 100;
      if (this.limitPercent > 100) {
        this.errors.push("Limit cannot be more than claimant demand!");
        return false;
      }
      if (this.yourLimit < 1) {
        this.errors.push("all values must be positive");
        return false;
      }
      if (this.demand < 1) {
        this.errors.push("all values must be positive");
        return false;
      }
      if (this.yourStart < 1) {
        this.errors.push("all values must be positive");
        return false;
      }
      return true;
    },
    validateOffer() {
      if (this.offer < 1) {
        this.errors.push("offer amount must be positive");
        return false;
      }
      return true;
    },

    validateDemand() {
      if (this.demand < 1) {
        this.errors.push("demand amount must be positive");
        return false;
      }
      return true;
    },
    addOffer() {
      this.error = false;
      this.errors = [];
      if (!this.validateOffer()) {
        this.error = true;
        return false;
      }
      this.userBarStyle.width =
        parseFloat(this.offer / this.demands[0]) * 100 + "%";
      this.offers.push(this.offer);
    },

    addDemand() {
      this.error = false;
      this.errors = [];
      if (!this.validateDemand()) {
        this.error = true;
        return false;
      }
      this.claimantBarStyle.width =
        parseFloat(this.demand / this.demands[0]) * 100 + "%";
      this.claimantBarStyle.left = parseFloat(
        100 - (this.demand / this.demands[0]) * 100
      );
      this.demands.push(this.demand);
    },
  },
};
</script>
