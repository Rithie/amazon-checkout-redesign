<template>
  <div style="max-height: 100vh; overflow: auto;">
    <!--HEADER-->
    <div class="padding-hor-l padding-vert-m black flexy" style="height: 80px; background-color: #232f3e;">
      <div>
        <button @click="currentView = 'Start'" style="color: white;">Amazon</button>
      </div>
      <div class="fixed" style="width: 40px;">
        <button @click="currentView = 'ShoppingCart'" style="color: white;">Cart</button>
      </div>
    </div>

    
    <!--START--><Start v-if="currentView === 'Start'"/>
    <!--SHOPPING CART--><ShoppingCart   v-if="currentView === 'ShoppingCart'"
                    :cart="cart"
                    :alsoItems="alsoItems"
                    :recentItems="recentItems"
                    :extraItems="extraItems"
                    @confirmed="shoppingCartConfirmed($event)"/>
    <!--SHIPPING ADDRESS--><ShippingAddress  v-if="doShowAddressManager"
                      :mode="addressEditorMode"
                      @confirmed="shippingAddressConfirmed($event)"
                      @cancelled="shippingAddressCancelled()"/>
    <!--PAYMENT METHOD--><PaymentMethod  v-if="currentView === 'PaymentMethod'"
                    @confirmed="paymentMethodConfirmed()"
                    @cancelled="paymentMethodCancelled()"/>
    <!--EXTRA SIGNUP--><ExtraSignup  v-if="currentView === 'ExtraSignup'"/>

    <!--CHECKOUT--><div id="checkout" v-if="currentView === 'Checkout'">
      <div class="padding-hor-l">
        <div class="padding-vert-l flexy">
          <h1>Checkout</h1>
        </div>
        <div class="flexy align-top">
          <div>
            <div class="flexy align-stretch">
              <!--SHIPPING ADDRESS MIN-->
              <div class="bordered rounded padding-vert-m padding-hor-l fixed" style="width: 49%" :class="{pushedBack: !isPaymentFilled && isShippingFilled}">
                <ShippingAddressMin :selectedInfo="selectedShippingInfo" @edit="editAddressPressed($event)"/>
              </div>
              <div class="fixed" style="width: 2%"></div>
              <!--PAYMENT METHOD MIN-->
              <div class="bordered rounded padding-vert-m padding-hor-l fixed" style="width: 49%;" :class="{pushedBack: !isShippingFilled}">
                <PaymentMethodMin :isPaymentFilled="isPaymentFilled" @edit="currentView = 'PaymentMethod'"/>
              </div>
            </div>
            <div class="padding-vert-xs"></div>
            <div class="flexy align-stretch">
              <!--SHOPPING CART MIN-->
              <div class="bordered rounded padding-vert-m padding-hor-l fixed" style="width: 49%;" :class="{pushedBack: !isPaymentFilled || !isShippingFilled}">
                <ShoppingCartMin  :cart="cart"
                                                        @edit="currentView = 'ShoppingCart'"/>
              </div>
              <div class="fixed" style="width: 2%"></div>
              <!--DELIVERY MIN-->
              <div class="bordered rounded padding-vert-m padding-hor-l fixed" style="width: 49%;" :class="{pushedBack: !isPaymentFilled || !isShippingFilled}">
                <Delivery :chosenDelivery="chosenDelivery"/>
              </div>
            </div>
            
          </div>
          <div class="fixed" style="width: 24px;"></div>
          <!--SUMMARY-->
          <div class="padding-hor-l padding-vert-m fixed silver rounded" style="width: 300px;" :class="{pushedBack: !isPaymentFilled || !isShippingFilled}">
            <OrderSummary   :isShippingFilled="isShippingFilled"
                                        :isPaymentFilled="isPaymentFilled"
                                        @placed="currentView = 'ExtraSignup'"/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Start from './Start'
import ShoppingCart from './ShoppingCart'
import ShoppingCartMin from './ShoppingCartMin'
import Delivery from './Delivery'
import ShippingAddress from './ShippingAddress'
import ShippingAddressMin from './ShippingAddressMin'
import PaymentMethod from './PaymentMethod'
import PaymentMethodMin from './PaymentMethodMin'
import OrderSummary from './OrderSummary'
import ExtraSignup from './ExtraSignup'

export default {
  components: {
    Start,
    ShoppingCart,
    ShoppingCartMin,
    ShippingAddress,
    ShippingAddressMin,
    PaymentMethod,
    PaymentMethodMin,
    Delivery,
    OrderSummary,
    ExtraSignup
  },
  data () {
    return {
      currentView: 'Start',
      chosenDelivery: '1',
      //
      isShippingFilled: false,
      selectedShippingInfo: null,
      //
      isPaymentFilled: false,
      //
      doShowAddressManager: false,
      addressEditorMode: 'edit',
      cart: [
        {
          name: 'Beautiful Evidence',
          by: 'Edward R. Tufte',
          img: require('./../../../assets/products/evidence.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 28.87,
          quantity: 1
        },
        {
          name: 'Hooked: How to Build Habit-Forming Products',
          by: 'Nir Eyal',
          img: require('./../../../assets/products/hooked.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 18.56,
          quantity: 1
        }
      ],
      alsoItems: [
        {
          name: 'Sprint: How to Solve Big Problems and Test New Ideas in Just Five Days',
          by: 'Jake Knapp',
          img: require('./../../../assets/products/sprint.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 14.53,
          quantity: 1
        },
        {
          name: 'The Lean Startup: How Today\'s Entrepreneurs Use Continuous Innovation to Create Radically Successful Businesses',
          by: 'Eric Ries',
          img: require('./../../../assets/products/lean.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 20.29,
          quantity: 1
        },
        {
          name: 'The Hard Thing About Hard Things: Building a Business When There Are No Easy Answers',
          by: 'Ben Horowitz',
          img: require('./../../../assets/products/hard.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 19.66,
          quantity: 1
        },
        {
          name: 'Rework',
          by: 'Jason Fried',
          img: require('./../../../assets/products/rework.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 17.68,
          quantity: 1
        },
        {
          name: 'Remote: Office Not Required',
          by: 'Jason Fried',
          img: require('./../../../assets/products/remote.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 15.59,
          quantity: 1
        }
      ],
      recentItems: [
        {
          name: 'Envisioning Information',
          by: 'Edward R. Tufte',
          img: require('./../../../assets/products/envision.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 34.96,
          quantity: 1
        },
        {
          name: 'About Face: The Essentials of Interaction Design',
          by: 'Alan Cooper',
          img: require('./../../../assets/products/face.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 36.47,
          quantity: 1
        },
        {
          name: '100 Things Every Designer Needs to Know About People',
          by: 'Susan Weinschenk',
          img: require('./../../../assets/products/100.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 22.95,
          quantity: 1
        },
        {
          name: 'HTML and CSS: Design and Build Websites',
          by: 'Jon Duckett',
          img: require('./../../../assets/products/html.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 20.53,
          quantity: 1
        }
      ],
      extraItems: [
        {
          name: 'CSS: The Missing Manual',
          by: 'David Sawyer McFarland',
          img: require('./../../../assets/products/css.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 24.61,
          quantity: 1
        },
        {
          name: 'The Principles of Beautiful Web Design: Designing Great Web Sites is Not Rocket Science!',
          by: 'Jason Beaird',
          img: require('./../../../assets/products/beauty.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 31.23,
          quantity: 1
        },
        {
          name: 'Logo Design Love: A Guide to Creating Iconic Brand Identities, 2nd Edition',
          by: 'David Airey',
          img: require('./../../../assets/products/logo.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 31.05,
          quantity: 1
        },
        {
          name: 'Layout Essentials: 100 Design Principles for Using Grids (Design Essentials)',
          by: 'Beth Tondreau',
          img: require('./../../../assets/products/layout.jpg'),
          seller: 'Amazon',
          availability: 'In Stock',
          price: 14.76,
          quantity: 1
        }
      ]
    }
  },
  methods: {
    shoppingCartConfirmed (items) {
      this.currentView = 'Checkout'
      this.cart = items
    },
    shippingAddressConfirmed (info) {
      this.isShippingFilled = true
      this.selectedShippingInfo = info
      // this.currentView = 'Checkout'
      this.doShowAddressManager = false
    },
    shippingAddressCancelled () {
      // this.currentView = 'Checkout'
      this.doShowAddressManager = false
    },
    paymentMethodConfirmed () {
      this.isPaymentFilled = true
      this.currentView = 'Checkout'
    },
    paymentMethodCancelled () {
      this.currentView = 'Checkout'
    },
    editAddressPressed (data) {
      if (data === 'editor') {
        this.addressEditorMode = 'edit'
        this.doShowAddressManager = true
      } else {
        this.addressEditorMode = 'manage'
        this.doShowAddressManager = true
      }
    }
  },
  created () {
    window.scrollTo(0, 0)
  }
}
</script>
<style lang="scss" scoped>
.pushedBack {
  opacity: 0.4;
}
</style>
