<script>
    import { onMount } from 'svelte';
  
    // State for loading, iframe URL, and errors
    let isLoading = true;
    let iframeSrc = '';
    let error = '';
  
    // Fetch the iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/simple-iframe', {
          method: 'POST', // Match the method in your API route
          headers: {
            'Content-Type': 'application/json',
          },
        });
  
        if (!response.ok) {
          const errorData = await response.json();
          throw new Error(errorData.error || 'Failed to fetch iframe URL');
        }
  
        const data = await response.json();
        console.log(data)
        // Ensure the key matches the API response (iframeSrc)
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
        This embed defaults to the Explore view. There are no row access policies enabled so your end user is able to access all the available data in the dashboard.
        <br><br> There are also no limitations on the navigation within this specific explore dashboard. This means the user can navigate to the Pivot view, as well.
    </div>
</div>
