<script>
    import { onMount } from 'svelte';
  
    let isLoading = true;
    let iframeSrc = '';
    let error = '';
  
  
    // Fetch the initial iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/navigation-iframe', {
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
  
      } catch (err) {
        error = err.message;
      } finally {
        isLoading = false;
      }
    });
  </script>
    
    <style>
    </style>
    
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
        This is a simple embed with navigation enabled. This allows your user to view all of the explore dashboards and their views. (Explore, Pivot, TDD). Refreshing the page will returen to the initial embed page.
      </div>
  </div>
  