<template>
    <a :href="addToCartURL" target="_blank">Add To Cart</a>
  </template>
  
  <script>
  export default {
    props: {
      productId: Number,
    },
    computed: {
      addToCartURL() {
        return `https://greet.bg/?add-to-cart=${this.productId}`;
      },
    },
  };
  </script>
  
  <style scoped>
  a{
    border: 2px solid #ffd7ba;
    border-radius: 8px;
    padding: 5px;
    text-decoration: none;
    background: #fec5bb;
    color: inherit;
  }
  </style>
  