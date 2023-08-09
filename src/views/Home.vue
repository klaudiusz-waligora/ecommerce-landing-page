<template>
  <nav>
    <header class="flex">
      <div id="pageMenu">
        <!-- hamburger menu button -->
        <button>
          <img
            src="../assets/icon-menu.svg"
            alt="mobile-menu"
            id="hamburgerIcon"
            @click="
              () => {
                toggleOverlay();
                toggleMenu();
                if (isCartActive) {
                  toggleCart();
                }
              }
            "
          />
        </button>
        <!-- screen overlay when mobile menu active -->
        <div
          id="screenOverlay"
          class="displayNone"
          :class="{ displayBlock: isMenuActive }"
        ></div>
        <div
          id="mobileMenuOverlay"
          class="displayNone"
          :class="{ displayBlock: isMenuActive }"
        >
          <img
            src="../assets/icon-close.svg"
            alt="icon-close"
            id="iconClose"
            @click="
              () => {
                toggleOverlay();
                toggleMenu();
              }
            "
          />
          <!-- mobile navigation menu -->
          <div id="mobileNav">
            <ul id="mobileNav">
              <li @click="toggleMenu">collections</li>
              <li @click="toggleMenu">men</li>
              <li @click="toggleMenu">women</li>
              <li @click="toggleMenu">about</li>
              <li @click="toggleMenu">contact</li>
            </ul>
          </div>
        </div>
        <img src="../assets/logo.svg" alt="logo" id="mobileLogo" />

        <!-- desktop navigation menu -->
        <ul id="desktopNav">
          <img src="../assets/logo.svg" alt="logo" id="desktopLogo" />
          <li class="underline">collections</li>
          <li class="underline">men</li>
          <li class="underline">women</li>
          <li class="underline">about</li>
          <li class="underline">contact</li>
        </ul>
      </div>

      <!-- user menu -->
      <div id="userMenu">
        <button>
          <img
            src="../assets/icon-cart.svg"
            alt="cart-icon"
            @click="toggleCart"
            id="cartIcon"
          />
        </button>
        <div
          id="numberOfItemsInCart"
          class="displayNone"
          :class="{ displayBlock: quantity > 0 }"
        >
          {{ quantity }}
        </div>

        <button>
          <img src="../assets/image-avatar.png" alt="avatar" id="avatar" />
        </button>
      </div>
    </header>
  </nav>

  <div
    id="cartItems"
    class="displayNone active"
    :class="{ displayBlock: isCartActive }"
  >
    <div id="cartContent">
      <h3>Cart</h3>
      <div id="separator"></div>
      <div id="emptyCartContent">
        <h2
          id="emptyCartMsg"
          class="displayNone"
          :class="{ displayBlock: !quantity || quantity < 0 }"
        >
          Your cart is empty.
        </h2>
      </div>
      <div
        id="notEmptyCartContent"
        class="displayNone"
        :class="{ displayFlex: quantity > 0 }"
      >
        <div id="itemAvatar">
          <img
            src="../assets/image-product-1-thumbnail.jpg"
            alt="item-avatar"
          />
        </div>
        <div id="itemSummary">
          <h4>Fall Limited Edition Sneakers</h4>
          <p id="price">
            $125.00 x {{ quantity }}
            <span id="multipiedPrice">${{ 125 * quantity }}</span>
          </p>
        </div>
        <div id="deleteIcon" @click="deleteItem">
          <img src="../assets/icon-delete.svg" alt="delete-icon" />
        </div>
      </div>
      <button
        id="checkoutBtn"
        class="displayNone"
        :class="{ displayBlock: quantity > 0 }"
      >
        Checkout
      </button>
    </div>
  </div>

  <!-- FULL SCREEN GALLERY -->
  <div id="fullScreenGallery" :style="displayBlock">
    <button id="previousBtn" class="lrBtn" @click="prevImage"></button>
    <button id="nextBtn" class="lrBtn" @click="nextImage"></button>
    <button id="closeBtn" @click="closeGallery">
      <img src="../assets/icon-close.svg" />
    </button>
    <img
      :src="mainImageSrc"
      alt="product-image"
      class="displayNone"
      id="fullScreenImage"
    />
  </div>
  <!-- end of full screen gallery -->

  <!-- product images section -->
  <div id="itemContainer">
    <article>
      <div id="productImageContainer">
        <div class="productMainImage">
          <img
            :src="mainImageSrc"
            alt="product-image"
            id="mainImg"
            @click="openGallery"
          />

          <button id="previousBtn" class="lrBtn" @click="prevImage"></button>
          <button id="nextBtn" class="lrBtn" @click="nextImage"></button>
        </div>
        <div id="otherImages">
          <img
            v-for="(thumbnail, index) in thumbnails"
            :src="thumbnail"
            :alt="'img' + (index + 1) + 'thumbnail'"
            :class="{ activeImage: mainImageSrc == mainImages[index] }"
            @click="changeMainImage(thumbnail)"
            :key="index"
          />
        </div>
      </div>
    </article>
    <!-- end of product image section -->

    <!-- product about section -->
    <article id="productAbout">
      <h3 class="mb">sneaker company</h3>
      <h2 class="mb">
        fall limited edition <span id="breakLine">sneakers</span>
      </h2>
      <p id="productDescription" class="mb-2">
        These low-profile sneakers are your perfect casual wear companion.
        Featuring adurable rubber outer sole, they’ll withstand everything the
        weather can offer.
      </p>

      <div id="priceContainer" class="mb-2">
        <div class="flex">
          <div id="currentPrice">$125.00</div>
          <div id="discount">50%</div>
        </div>
        <div id="previousPrice">$250.00</div>
      </div>

      <div id="buttonsContainer">
        <div id="quantityContainer" class="mb">
          <span id="quantity">{{ tempQuantity }}</span>
          <button id="minusBtn" @click="decrementQuantity"></button>
          <button id="plusBtn" @click="incrementQuantity"></button>
        </div>

        <button
          id="addToCart"
          @click="
            () => {
              updateQuantity();
              if (!isCartActive && quantity > 0) {
                toggleCart();
              }
            }
          "
        >
          <img
            src="../assets/icon-cart.svg"
            alt="cart-icon"
            id="cart-icon"
          />Add to cart
        </button>
      </div>
    </article>
    <!-- end of product about -->
  </div>
</template>

<script>
import "../styles/general.css";
import "../styles/navbar.css";
import "../styles/product.css";
import "../styles/desktop.css";

export default {
  data() {
    return {
      isMenuActive: false,
      isOverlayActive: false,
      isCartActive: false,
      isGalleryOpen: false,
      displayBlock: {
        display: "none",
      },
      tempQuantity: 0,
      quantity: 0,
      tempIndex: 0,
      mainImageSrc: require("../assets/image-product-1.jpg"),
      mainImages: [
        require("../assets/image-product-1.jpg"),
        require("../assets/image-product-2.jpg"),
        require("../assets/image-product-3.jpg"),
        require("../assets/image-product-4.jpg"),
      ],
      thumbnails: [
        require("../assets/image-product-1-thumbnail.jpg"),
        require("../assets/image-product-2-thumbnail.jpg"),
        require("../assets/image-product-3-thumbnail.jpg"),
        require("../assets/image-product-4-thumbnail.jpg"),
      ],
    };
  },

  methods: {
    toggleMenu() {
      this.isMenuActive = !this.isMenuActive;
    },
    toggleOverlay() {
      this.isOverlayActive = !this.isOverlayActive;
    },
    toggleCart() {
      this.isCartActive = !this.isCartActive;
    },
    incrementQuantity() {
      this.tempQuantity++;
    },
    decrementQuantity() {
      this.tempQuantity--;
    },
    updateQuantity() {
      this.quantity = this.tempQuantity;
    },
    deleteItem() {
      this.quantity = 0;
    },
    changeMainImage(src) {
      const index = this.thumbnails.indexOf(src);
      if (index !== -1) {
        this.mainImageSrc = this.mainImages[index];
      }
    },
    prevImage() {
      this.mainImageSrc = this.mainImages[this.tempIndex];
      this.tempIndex--;
      if (this.tempIndex < 0) {
        this.tempIndex = 3;
      }
    },
    nextImage() {
      this.mainImageSrc = this.mainImages[this.tempIndex];
      this.tempIndex++;
      if (this.tempIndex > 3) {
        this.tempIndex = 0;
      }
    },
    openGallery() {
      this.isGalleryOpen = !this.isGalleryOpen;
      this.displayBlock.display = "block";
    },
    closeGallery() {
      this.isGalleryOpen = !this.isGalleryOpen;
      this.displayBlock.display = "none";
    },
  },
};
</script>
