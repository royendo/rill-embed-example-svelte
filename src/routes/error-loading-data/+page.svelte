<script>
    import { onMount } from 'svelte';
  
    let isLoading = true;
    let iframeSrc = '';
    let error = '';

  
    // Fetch the initial iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/no-data-iframe', {
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
    .container {
      display: flex;
      background-color: #f9f9f9;
    }
  
    .iframe-container {
      width: 100%;
      max-width: 100%;
      height: 1200px;
      max-height: 100%;
      background-color: #ffffff;
      border-radius: 8px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
      overflow: hidden;
    }
  
      .footer {
        width: 100%;
        max-width: 100%;
        height: 200px;
        min-height: 200px;
        background-color: #ffffff;
        border-radius: 8px;
        box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        overflow: hidden;
        padding: 20px;
        margin: 20px;
      }
    
      .iframe {
        width: 100%;
        height: 100%;
        border: none;
      }
    
      .loading,
      .error {
        font-size: 1.5rem;
        color: #666;
      }
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
            In the case that your dashboard returns empty due to no available data based on the row access policies that you have defined, you will see this page. 
        </div>
  </div>
  