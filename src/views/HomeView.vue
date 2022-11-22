<template>
  <div>
    <header class="header">
        <h1 id="headText" style="text-align: center;">
            Welcome to Burger Store!
        </h1>
        <img id="headPic" src=https://wallpapercave.com/wp/wp1874155.jpg alt="restaurant">
    </header>

     <main>
        <section class="burger">
            <h3 id="bodyText">Select burger</h3>
            <p id="bodyText">Below is where you execute burger selection</p>

        <div class="wrapper">
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger" 
                  v-bind:key="burger.name"
                  v-on:orderedBurger="addToOrder($event)"/>
        </div>
      
        </section>

        <section class="contact" id="bodyText">
            <h3>Customer information</h3>
            <p>Don't forget to follow us on social media.</p>

            <h3>Delivery information</h3>
            <form>
                <p>
                    <label for="Full name">Full name</label><br>
                    <input type="text" id="fullname" v-model="fn" required="required" placeholder="First- and last name">
                </p>
                <p>
                    <label for="email">E-mail</label><br>
                    <input type="email" id="email" v-model="em" placeholder="Email-adress">
                </p>
                <!--
                  <p>
                      <label for="street">Street</label><br>
                      <input type="text" id="street" v-model="st" required="required" placeholder="Street name">
                  </p>
                  <p>
                      <label for="house">House</label><br>
                      <input type="text" id="house" v-model="he" required="required" placeholder="House number">
                  </p>
                -->
                <p>
                    <label for="payment">Payment method</label><br>
                    <select id="payment" v-model="pm">
                        <option>Klarna</option>
                        <option>Mastercard</option>
                        <option>Swish</option>
                        <option>Paypal</option>
                    </select>
                 </p>

                 <p>Gender</p>
                 <p>
                    <input type="radio" id="male" v-model="gender" value="Male">
                    <label for="male">Male</label>
                 </p>
                 <p>
                    <input type="radio" id="female" v-model="gender" value="Female">
                    <label for="female">Female</label>
                 </p>
                 <p>
                    <input type="radio" id="non-binary" v-model="gender" value="Non-binary">
                    <label for="non-binary">Non-binary</label>
                 </p>
                 <p>
                    <input type="radio" id="undisclosed" v-model="gender" value="Undisclosed">
                    <label for="undisclosed">Undisclosed</label>
                 </p>
            </form>

            <h4> Please mark your address below:</h4>

            <div id="sizeMap">
               <div id="map" v-on:click="addOrder">
                  <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
                   T
                  </div>
                </div>
            </div>

            <div>
              <button class="btnOrder" id ="btnOrder" v-on:click="printOrder" type="submit" >
               <img src="https://png.pngitem.com/pimgs/s/160-1604188_hamburger-letters-png-burger-and-fries-icon-transparent.png"
               width = "25" height = "25">
               Place your order
             </button>
            </div>
        </section>
     </main>

     <hr>
     <footer>
        &COPY; 2022 Burger Store AB by Emil Vendlegård
     </footer>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

//JSON menu as constructor


/*
//Constructor
let MenuItem = function(name, kCal, img, lactose, gluten){
  this.name = name;
  this.kCal = kCal;
  this.img = img;
  this.lactose = lactose;
  this.gluten = gluten;
}
const burger1 = new MenuItem("Umami Bacon Burger", "788", "https://www.max.se/contentassets/297c9781f4e34318ab63df3535fa1561/product_gdl-umami-bacon-burger2.png?width=668&height=622&sharpen=5&sigma=1,4&threshold=0", true, true)
const burger2 = new MenuItem("Cheeseburger","317", "https://www.max.se/contentassets/2c64a1d06cc64c9284fbfbca16d37f59/product_cheeseburgare.png?width=1160&sharpen=5&sigma=1,4&threshold=0", true, true)
const burger3 = new MenuItem("Salad Wrap Burger", "538", "https://www.max.se/contentassets/a8b6e8662e6d4a2ba06592874b6e5c13/product_salad-wrap-burgare.png?width=668&height=622&sharpen=5&sigma=1,4&threshold=0", true, true) 
let burgerArray = [burger1, burger2, burger3];
*/


export default {
  name: 'HomeView',
  components: {
    Burger
  },

  data: function () {
    return {
      // burgers: [ {name: "small burger", kCal: 250},
      //           {name: "standard burger", kCal: 450},
      //            {name: "large burger", kCal: 850}
      //          ]
         burgers: menu,
         fn: '',
         em: '',
         pm: '',
         gender: '',

         orderedBurger:{},

         location: {x: 0, y: 0}
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      // socket.emit("addOrder", { orderId: this.getOrderNumber(),
      //                           details: { x: event.clientX - 10 - offset.x,
      //                                      y: event.clientY - 10 - offset.y },
      //                           orderItems: ["Beans", "Curry"]
      //                         }
      //            );
                 this.location.x=event.clientX - 10 - offset.x;
                 this.location.y=event.clientY -10 - offset.y;
    },

    printOrder: function(){
      console.log(this.fn, this.em, this.pm, this.gender);
      console.log(this.orderedBurger);

      socket.emit("addOrder", {orderId: this.getOrderNumber(),
        details:{
          x: this.location.x,
          y: this.location.y
        },
        orderItems: this.orderedBurger,
        customerInformation: [this.fn, this.em, this.pm, this.gender]
      });
    },
    addToOrder: function (event) {
     this.orderedBurger[event.name] = event.amount;

    },
    setLocation: function(event){
     this.location.x=event.clientX - 10 - event.currentTarget.getBoundingClientRect().left;
     this.location.y=event.clientY - 10 - event.currentTarget.getBoundingClientRect().top;
    }
  }
}
</script>

<style>
  body {
    font-family: arial;
 }

 section{
   margin: 10px;
   border: 2px dashed
 }

 div {
   margin: 20px;
   padding-right: 10px;
 }

 .btnOrder{
   margin: 10px;
 }
 .btnAmount{
  margin-bottom:10px
  
 }
 #btnOrder:hover{
   background-color: lawngreen;
 }
 #btnIncrease:hover{
   background-color: lawngreen;
 }
 #btnDecrease:hover{
   background-color: red;
 }

 .textcolor{
    color: white;
 }
 #bold{
    font-weight: bold;
 }

 .header{
   color: white;
   margin: 10px;
   width: 100%;
   height: 200px;
   overflow:hidden
 }
 #headText{
  position: absolute;
  padding-left: 15px;
  -webkit-text-stroke: 1px black;
 }
 #headPic{
   opacity: 1;
 }

 .burger{
    width: 100%;
    background-color: black;
    color: white;
 }
 #bodyText{
  padding-left: 15px;
 }

 .contact{
   width: 100%;
   
 }

#sizeMap{
  width: 900px;
  height: 500px;
  overflow:scroll
}

#map {
  position: relative;
  width: 1920px;
  height: 1078px;
  background: url("C:/Users/emilv/OneDrive/Skrivbord/Repositories/1md031-lab-2022/public/img/polacks.jpg");
  cursor: pointer;
}

#map div{
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
    margin: 0px;
    padding: 0px;
  }

.wrapper {
  display: grid;
  grid-gap: 40px;
  grid-template-columns: 400px 400px 400px;
  background-color: black;
}

.box {
  background-color: rgb(46, 45, 45);
  color: white;
  border-radius: 5px;
  padding: 35px;
  font-size: 100%;
}

#burgerPic{
  width: 290px;
  height: 270px;
}

.allergies{
  color: white;
  font-weight: bold;
}
</style>