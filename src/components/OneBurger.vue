<template>
    <div class="box">
      <h4 style="text-align: center;">{{burger.name}} {{burger.price}}</h4>
      Order here:
      <button class="btnAmount" id="btnIncrease" v-on:click="increase">
      Add
      </button>
      <button class="btnAmount" id="btnDecrease" v-on:click="decrease">
       Delete 
      </button>
      
      <h5 style="text-align: center;"> Total amount ordered:
      {{amountOrdered}}</h5>

      <img id="burgerPic" v-bind:src="burger.img"/>

      <ul>
        <li v-if="burger.name=='Umami Bacon Burger,'"> Contains bacon </li>
        <li v-if="burger.name=='Umami Bacon Burger,'"> 788 kCal </li>
        <li v-if="burger.name=='Cheeseburger,'"> 317 kCal</li>
        <li v-if="burger.name=='Salad Wrap Burger,'"> Contains eggs and mustard </li>
        <li v-if="burger.name=='Salad Wrap Burger,'"> 538 kCal </li>
        <li v-if="burger.lactose"><span class="allergies"> Lactose </span></li>
        <li v-if="burger.gluten"><span class="allergies"> Gluten </span></li>
      </ul>

    </div>
  </template> 
  
  <script>
  export default {
    name: 'OneBurger',
    props: {
      burger: Object
    },
    
  data: function () {
      return {
        amountOrdered: 0,
      }
    },
  methods: {
    addBurger: function(){
      this.$emit('orderedBurger',
      {name: this.burger.name, 
        amount: this.amountOrdered});
      },

    increase: function(){
      this.amountOrdered += 1
      this.$emit('orderedBurger',
      {name: this.burger.name, 
          amount: this.amountOrdered});
  },
    decrease: function(){
      this.amountOrdered -= 1
      this.$emit('orderedBurger',
      {name: this.burger.name, 
          amount: this.amountOrdered});
    }
  }
}

  
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  
  </style>
  