<script>
export default {
  data() {
    return {
      orders: [
        {
          title: "T-Shirt",
          quantity: 3,
          price: 350,
          category: "Clothing",
          image: "../../public/image/t-shirt.jpeg",
        },
        {
          title: "Hat",
          quantity: 2,
          price: 250,
          category: "Accessories",
          image: "../../public/image/hat.jpeg",
        },
        {
          title: "Bag",
          quantity: 1,
          price: 640,
          category: "Bag",
          image: "../../public/image/bag.jpeg",
        },
        {
          title: "Watch",
          quantity: 1,
          price: 850,
          category: "Accessories",
          image: "../../public/image/watch.jpeg",
        },
        {
          title: "Belt",
          quantity: 2,
          price: 230,
          category: "Accessories",
          image: "../../public/image/belt.jpeg",
        },
      ],
      dropdownCoupon: false,
      dropdownOnTop: false,
      dropdownSeasonal: false,
      Amount: 50,
      Percentage: 0.9,
      CategoryClothing: 0.85,
      CategoryAccessories: 0.9,
      totalPoints: 1000,
      subPoints: 1000,
      CustomerPoints: 1000,
      PointOntop: 68,
      DiscountPoint: null,
      Every300THB: 300,
      Discount40THB: 40,
      checkCoupon: false,
      checkOntop: false,
      checkSeasonal: false,
      priceByCoupon: 0,
      priceByOntop: 0,
      priceBySeasonal: 0,
      selectedCoupon: "",
      selectedOntop: "",
      selectSeasonal: false,
    };
  },
  methods: {
    togglePaymentPopup() {
      if (
        this.checkCoupon == true &&
        this.checkOntop == true &&
        this.checkSeasonal == true
      ) {
        alert("Payment success!");
      } else {
        alert("Please check campaign again!");
      }
    },
    subTotal() {
      const subTotal = this.orders.reduce((total, order) => {
        return total + order.quantity * order.price;
      }, 0);
      return subTotal;
    },
    CouponType(Coupon) {
      this.checkCoupon = true;
      this.selectedCoupon = Coupon;
      this.totalPoints = this.subPoints;
      this.selectedOntop = "";
      this.selectSeasonal = false;
      if (Coupon === "fixedAmount") {
        this.priceByCoupon = this.subTotal() - this.Amount;
      } else if (Coupon === "percentageDiscount") {
        this.priceByCoupon = this.subTotal() * this.Percentage;
      } else {
        this.priceByCoupon = this.subTotal();
      }
      this.checkCoupon = true;
      this.checkOntop = false;
      this.checkSeasonal = false;
      return this.priceByCoupon;
    },
    OnTopType(ontop) {
      if (!this.checkCoupon) {
        alert("Please select a coupon first");
        return;
      }
      this.selectedOntop = ontop;
      this.selectSeasonal = false;
      if (ontop === "Clothing") {
        this.checkOntop = true;
        const clothingTotal = this.orders.reduce((total, order) => {
          if (order.category === "Clothing") {
            let clothingTotal1 = order.quantity * order.price;
            this.priceByOntop =
              this.priceByCoupon - clothingTotal1 * this.CategoryClothing;
            this.totalPoints = this.subPoints;
            return this.priceByOntop;
          }
        }, 0);
      } else if (ontop === "Accessories") {
        this.checkOntop = true;
        const accessoriesTotal = this.orders.reduce((total, order) => {
          if (order.category === "Accessories") {
            let accessoriesTotal1 = order.quantity * order.price;
            this.priceByOntop =
              this.priceByCoupon - accessoriesTotal1 * this.CategoryAccessories;
            this.totalPoints = this.subPoints;
            return this.priceByOntop;
          }
        }, 0);
      } else if (ontop === "Point") {
        this.checkOntop = true;
        const cappedPrice = Math.floor(this.subTotal() * 0.2);
        this.priceByOntop = this.priceByCoupon;

        if (
          this.CustomerPoints > cappedPrice &&
          cappedPrice > this.PointOntop
        ) {
          this.CustomerPoints = cappedPrice;
          if (this.CustomerPoints > this.PointOntop) {
            this.CustomerPoints = this.PointOntop;
          }
        } else if (
          this.CustomerPoints > cappedPrice &&
          cappedPrice < this.PointOntop
        ) {
          this.CustomerPoints = this.PointOntop;
          if (this.CustomerPoints > cappedPrice) {
            this.CustomerPoints = cappedPrice;
          }
        }
        this.totalPoints = this.totalPoints - this.CustomerPoints;
        this.priceByOntop = this.priceByCoupon - this.CustomerPoints;
        return this.priceByOntop;
      }
      this.checkOntop = true;
      this.checkSeasonal = false;
      return this.priceByOntop;
    },
    Seasonal() {
      if (!this.checkCoupon && !this.checkOntop) {
        alert("Please select a Coupon and On Top first");
        return;
      }
      if (!this.checkCoupon) {
        alert("Please select a Coupon first");
        return;
      }
      if (!this.checkOntop) {
        alert("Please select a On Top first");
        return;
      }
      this.selectSeasonal = true;
      const every300 = Math.floor(this.subTotal() / this.Every300THB);
      const multiply = every300 * this.Discount40THB;
      this.priceBySeasonal = this.priceByOntop - multiply;
      this.checkSeasonal = true;
      return this.priceBySeasonal;
    },
    totalPrice() {
      let total = this.subTotal();
      if (this.checkCoupon == true) {
        total = this.priceByCoupon;
      }
      if (this.checkCoupon == true && this.checkOntop == true) {
        total = this.priceByOntop;
      }
      if (
        this.checkSeasonal == true &&
        this.checkCoupon == true &&
        this.checkOntop == true
      ) {
        total = this.priceBySeasonal;
      }
      return total;
    },
    getPrice(orders) {
      return orders.quantity * orders.price;
    },
    toggleDropdownOfCoupon() {
      this.dropdownCoupon = !this.dropdownCoupon;
    },
    toggleDropdownOfOnTop() {
      this.dropdownOnTop = !this.dropdownOnTop;
    },
    toggleDropdownOfSeasonal() {
      this.dropdownSeasonal = !this.dropdownSeasonal;
    },
  },
};
</script>

<template>
  <div class="md:w-[1600px]">
    <div class="bg-black shadow-lg">
      <div class="md:flex items-center justify-between py-5 px-8 md:px-12">
        <div class="flex justify-between items-center">
          <div class="text-2xl font-bold text-white md:text-3xl">
            <a>Shopping Cart</a>
          </div>
        </div>
      </div>
    </div>

    <div class="grid md:grid-cols-2">
      <div class="bg-white h-full">
        <div class="">
          <h2
            class="text-xl font-semibold text-gray-800 md:text-3xl py-5 mx-10"
          >
            My Orders
          </h2>
          <div class="h-full grid grid-cols-1">
            <div
              v-for="(order, index) in orders"
              :key="index"
              class="flex px-10 py-5"
            >
              <div class="h-28 w-28">
                <img :src="order.image" class="w-full h-full object-cover" />
              </div>
              <p class="text-lg mx-10">
                {{ order.title }}<br />
                x{{ order.quantity }}
              </p>

              <div class="flex justify-end flex-grow">
                <h1>{{ getPrice(order) }} THB</h1>
              </div>
            </div>
          </div>
        </div>
        <div class="flex justify-end mt-6 pb-10"></div>
      </div>

      <div class="bg-gray-100 h-full">
        <div class="flex px-10 mt-5">
          <div class="w-28">
            <p class="text-lg">Subtotal</p>
          </div>
          <div class="flex justify-end flex-grow">
            <h1 class="text-lg" v-if="!checkCoupon">{{ subTotal() }}</h1>
            <h1 class="text-lg text-gray-500 line-through" v-if="checkCoupon">
              {{ subTotal() }}
            </h1>
            <h1 class="text-lg ml-2">THB</h1>
          </div>
        </div>

        <div class="flex px-10 mt-5">
          <div class="w-36">
            <p class="text-lg">Customer Points</p>
          </div>
          <div class="flex justify-end flex-grow">
            <h1 class="text-lg text-black">{{ this.totalPoints }} Ponits</h1>
          </div>
        </div>

        <!-- Coupon  -->
        <div class="flex mt-5">
          <div class="relative w-full">
            <button
              class="h-16 w-full border-t-2 border-gray-500 text-black text-left pl-10 pr-8 text-lg flex justify-between items-center"
              @click="toggleDropdownOfCoupon"
            >
              <span>Select Coupon</span>
              <span class="inline-flex items-center">
                <svg
                  :class="{ 'transform rotate-180': dropdownCoupon }"
                  class="w-5 h-5 transition-transform duration-200"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M19 9l-7 7-7-7"
                  ></path>
                </svg>
              </span>
            </button>
            <ul
              v-if="dropdownCoupon"
              class="z-10 mt-1 w-full bg-white rounded-md"
            >
              <li
                @click="CouponType('fixedAmount')"
                :class="{ 'text-red': selectedCoupon === 'fixedAmount' }"
                class="py-2 px-10 cursor-pointer hover:bg-gray-100 flex text-lg"
              >
                Discount 50 THB
              </li>
              <li
                @click="CouponType('percentageDiscount')"
                :class="{ 'text-red': selectedCoupon === 'percentageDiscount' }"
                class="py-2 px-10 cursor-pointer hover:bg-gray-100 flex text-lg"
              >
                Discount 10%
              </li>
            </ul>
          </div>
        </div>

        <!-- On Top  -->
        <div class="flex">
          <div class="relative w-full">
            <button
              class="h-16 w-full border-t-2 border-gray-500 text-black text-left pl-10 pr-8 text-lg flex justify-between items-center"
              @click="toggleDropdownOfOnTop"
            >
              <span>Select On Top</span>
              <span class="inline-flex items-center">
                <svg
                  :class="{ 'transform rotate-180': dropdownOnTop }"
                  class="w-5 h-5 transition-transform duration-200"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M19 9l-7 7-7-7"
                  ></path>
                </svg>
              </span>
            </button>
            <ul
              v-if="dropdownOnTop"
              class="z-10 mt-1 w-full bg-white rounded-md"
            >
              <li
                @click="OnTopType('Clothing')"
                :class="{ 'text-red': selectedOntop === 'Clothing' }"
                class="py-2 px-10 cursor-pointer hover:bg-gray-100 flex text-lg"
              >
                Discount 15% Off on Clothing
              </li>
              <li
                @click="OnTopType('Accessories')"
                :class="{ 'text-red': selectedOntop === 'Accessories' }"
                class="py-2 px-10 cursor-pointer hover:bg-gray-100 flex text-lg"
              >
                Discount 10% Off on Accessories
              </li>
              <li
                @click="OnTopType('Point')"
                :class="{ 'text-red': selectedOntop === 'Point' }"
                class="py-2 px-10 cursor-pointer hover:bg-gray-100 flex text-lg"
              >
                Use 68 Points
              </li>
            </ul>
          </div>
        </div>

        <!-- Seasonal  -->
        <div class="flex">
          <div class="relative w-full">
            <button
              class="h-16 w-full border-y-2 border-gray-500 text-black text-left pl-10 pr-8 text-lg flex justify-between items-center"
              @click="toggleDropdownOfSeasonal"
            >
              <span>Select Seasonal</span>
              <span class="inline-flex items-center">
                <svg
                  :class="{ 'transform rotate-180': dropdownSeasonal }"
                  class="w-5 h-5 transition-transform duration-200"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M19 9l-7 7-7-7"
                  ></path>
                </svg>
              </span>
            </button>
            <ul
              v-if="dropdownSeasonal"
              class="z-10 mt-1 w-full bg-white rounded-md"
            >
              <li
                @click="Seasonal()"
                :class="{ 'text-red': selectSeasonal }"
                class="py-2 px-10 cursor-pointer hover:bg-gray-100 flex text-lg"
              >
                Discount 40 THB at every 300 THB
              </li>
            </ul>
          </div>
        </div>
        <div class="flex justify-end px-10 mt-10">
          <p class="text-lg">Total Price :</p>
          <p class="text-lg text-red-500 ml-3 font-semibold">
            {{ totalPrice() }} THB
          </p>
        </div>
        <div class="flex justify-end px-10 py-10">
          <button
            @click="togglePaymentPopup()"
            class="px-4 py-3 bg-gray-900 text-gray-200 text-lg font-semibold rounded hover:bg-gray-800"
          >
            Payment
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.text-red {
  color: rgb(239, 68, 68);
}
</style>
