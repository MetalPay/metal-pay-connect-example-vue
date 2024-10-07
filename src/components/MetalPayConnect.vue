<template>
    <div id="metal-pay-connect" class="flex w-full items-center justify-center">
      <!-- This div is where the Metal Pay SDK will render -->
    </div>
  </template>
  
  <script>
  import { ref, onMounted, onUnmounted } from 'vue';
  import { MetalPayConnect } from 'metal-pay-connect-js';
  
  export default {
    name: 'MetalPayConnect',
    setup() {
      const metalPayConnect = ref(null);
  
      onMounted(async () => {
        // Reference to the element in the DOM
        const metalPayConnectEl = document.getElementById('metal-pay-connect');
  
        // Fetch Metal Pay credentials from your server
        async function fetchMetalPayParams() {
          try {
            const response = await fetch('http://localhost:3005/v1/signature');
            const data = await response.json();
            return {
              apiKey: data.apiKey,
              signature: data.signature,
              nonce: data.nonce
            };
          } catch (error) {
            console.error('Error fetching Metal Pay signature:', error);
            return null;
          }
        }
  
        const config = await fetchMetalPayParams();
  
        if (config) {
          // Initialize the Metal Pay Connect SDK
          metalPayConnect.value = new MetalPayConnect({
            el: metalPayConnectEl,
            environment: 'dev',  // Set environment to 'preview', 'dev', or 'prod'
            params: {
              apiKey: config.apiKey,
              signature: config.signature,
              nonce: config.nonce,
              address: { 'xpr-network': 'johndoe' }, // address for the user
              networks: ['xpr-network']  // List of networks to enable
            }
          });
        }
      });
  
      // Cleanup the Metal Pay SDK when the component is unmounted
      onUnmounted(() => {
        if (metalPayConnect.value) {
          metalPayConnect.value.destroy();
        }
      });
  
      return {};
    }
  };
  </script>
  
  <style scoped>
  /* Add any specific styling for the component if needed */
  #metal-pay-connect {
    height: 300px;
    border: 1px solid #ccc;
  }
  </style>
  