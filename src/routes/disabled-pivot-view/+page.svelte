<script>
    import { onMount } from 'svelte';
  
    let isLoading = true;
    let iframeSrc = '';
    let error = '';
  
  
    // Fetch the initial iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/no-pivot-iframe', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
        });
  
        if (!response.ok) {
          const errorData = await response.json();
          throw new Error(errorData.error || 'Failed to fetch iframe URL');
        }
  
        const data = await response.json();
        iframeSrc = data.iframeSrc;
  
        if (!iframeSrc) {
          throw new Error('iframeSrc is missing from the API response');
        }
  
        // Set default parameters (e.g., "Explore View")
      } catch (err) {
        error = err.message;
      } finally {
        isLoading = false;
      }
    });
  </script>
    

    
  <!-- Render Loading State -->
  {#if isLoading}
  <div class="container">
    <p class="loading">Loading...</p>
  </div>
  
  <!-- Render Error State -->
  {:else if error}
  <div class="container">
    <p class="error">Failed with error: {error}</p>
  </div>
  
  <!-- Render Main Content -->
  {:else}
  <div class="container">
    <div class="iframe-container">
      <iframe
       title="Explore Dashboard"
        class="iframe"
        allow="clipboard-read; clipboard-write"
        src={iframeSrc}
      ></iframe>
    </div>
  </div>
  {/if}
  
  <div class="container">
    <div class="footer">
            In case that you want to disable the pivot view. This must be done on the explore dashboard level. Please see <a href= 'https://github.com/rilldata/rill-examples/blob/main/rill-openrtb-prog-ads/dashboards/pivot_disabled.yaml'> an example explore dashboard </a> from our demos project.
        
        Need to update the api/no-pivot-iframe.js
        </div>
  </div>
  