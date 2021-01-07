<template>
  <div class="widget" id="prorata">
    <section>
      <h2>Pro Rata Calculator</h2>
      This calculator allows you to calculate a pro-rata settlement on an
      insurance claim. <br />
      When there are two or more claimants and the sum of their claims <i>exceeds</i> the
      insured's limits of liability, this formula is used to distribute the
      available proportionally to the claimants. Each claimant is owed the percentage
      of the available limits that their damages bear to the sum of all the claimant's damages.<br/><br/>
    </section>
    <aside>
      <form id="numClaimants">
      <div class="form">
      Please enter amount of the policy limits
      <div>
      <input v-model.number="policyLimits" type="number" required="required" placeholder="policy limits">
      </div>
      </div> 
      
      <div class="form" id="damagesFields" v-for="(inputField, index) in claimants" :key="index">
        Please enter amount of claimant {{index+1}} damages
        <div>
          <input v-model.number.lazy="claimants[index].damages" :name="`claimants[${index}][damages]`" type="number" class="form-control" required="required">
      </div>
      
      </div>
      <div class="form" id="results">
        <br/>
        <button type="button" v-on:click="addClaimant">Add claimant</button>
        <button @click="deleteClaimant(index)">Delete Claimant</button>
      </div>
      </form>
    </aside>
    
    <section>
       <button v-on:click="calculateProRata">calculate</button>
       <p v-if="errors.length" class="errorArea">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors" :key="error">{{ error }}</li>
    </ul>
  </p>
       <div v-if="completed">
       <div v-for="(input, index) in claimants" :key="index">
         <b>claimant {{index+1}} percentage: {{parseInt(claimants[index].percent*100,10)}}% amount owed ${{claimants[index].amountOwed.toFixed(2)}} </b>
       </div>
       </div>
    </section>
  </div>
</template>

<style scoped>
section{
  text-align: left;
}

  #prorata {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  overflow: scroll;
}

button {
  background-color: #008cba;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 20px;
  margin : 15px;
}
div.form {
  border-bottom: 1px solid #A8DADC;
  padding: 5px;
  margin: 15px;
  width: 290px;
}
</style>

<script>

export default {
  name: "pr",
  
  
  data: () => ({
    totalDamages : 0,
    policyLimits : null,
    completed : false,
    errors : [],
    claimants: [
      {
        amountOwed : 0,
        percent : 0,
        damages: ""
      },
       {
        amountOwed : 0,
        percent : 0,
        damages: ""
      }
    ]
  }),

  computed : {
    damagesWithinLimits : function(){
      if(this.calcTotalDamages<this.policyLimits){
        return true;
      }
      else{
        return false;
      }
    },
    calcTotalDamages : function(){
      let total=0;
      for(var i=0;i<this.claimants.length;i++){
      total += this.claimants[i].damages;
     }
     return total;
    }
  },

  methods: {
    addClaimant () {
      this.claimants.push({
        amountOwed : 0,
        percent : 0,
        damages: '',
      })
    },
  /*
  *Removes claimant from claimant array and removes damages input field from template.
  */
   deleteClaimant(index) {
      this.claimants.splice(index,1)
      this.completed=false;
    },

  /*
  * Sums damages fields and calculates percentage each amount bears to total damages.
  * Multiplies the percent by policyLimits to determine amount each claimant is owed.
  * Displays results to the template.
  */
    calculateProRata () {
    
      this.totalDamages = 0;
     for(var i=0;i<this.claimants.length;i++){
      this.totalDamages += this.claimants[i].damages;
     }

       if (!this.validateInput()){
        return false;
      }
     
     for (var j=0;j<this.claimants.length;j++){
       this.claimants[j].percent = (this.claimants[j].damages/this.totalDamages).toFixed(2);
       this.claimants[j].amountOwed = parseFloat(this.claimants[j].percent * this.policyLimits,10.00);
     }
    this.completed = true;
    },

    /*
    * Checks for positive values in the policyLimits and damages fields.
    */
    validateInput() {
      this.errors = [];
      if (this.policyLimits<1 || this.policyLimits===null){
        this.errors.push("negative policy limits ? really ? policy limits must be greater than zero");
        this.completed=false;
        return false;
      }
     for(var i=0;i<this.claimants.length;i++){
      if(this.claimants[i].damages<0||this.claimants[i].damages===null){
        this.errors.push("damages must be greater than zero");
        this.completed=false;
        return false;
      }
      if (this.totalDamages<this.policyLimits){
       this.errors.push("damages within policy limits. no need to pro rate.");
       this.completed=false;
       return false;
     }
     }
     return true;
    }
  }
};
</script>

