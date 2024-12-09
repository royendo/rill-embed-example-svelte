<script>
    import { onMount } from 'svelte';
  
    let isLoading = true;
    let iframeSrc = '';
    let error = '';
  
    // Function to update query parameters in the iframe URL
    function updateIframeParams(params) {
      const newIframeSrc = new URL(iframeSrc);
  
      // Update multiple parameters
      for (const [key, value] of Object.entries(params)) {
        newIframeSrc.searchParams.set(key, value);
      }
  
      iframeSrc = newIframeSrc.toString(); // Update iframe source
    }
  
    // Fetch the initial iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/view-iframe', {
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
        updateIframeParams( { view: 'explore', tr: 'P3M', f: "author_name IN ('Alexey Milovidov')" });
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
          flex-direction: column;
          align-items: center;
          background-color: #f9f9f9;
          padding: 20px;
          gap: 20px;
        }
      
        
          .buttons {
          display: flex;
          gap: 10px;
          margin-bottom: 20px;
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
    <div class="buttons">
      <button on:click={() => updateIframeParams({ view: 'explore', tr: 'inf', f: "", compare_dim: "", compare_tr: ""})}> Reset </button>
      <button on:click={() => updateIframeParams({ view: 'explore', tr: 'P3M', compare_dim: 'author_name', f: "author_name IN ('Alexey Milovidov', 'Robert Schulze', 'Max Kainov')" })}> Compare Authors! </button>
      <button on:click={() => updateIframeParams({ view: 'explore', tr: 'P3M', compare_tr: 'rill-PP'})}> Compare Time!</button>
  
    </div>
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
        It is possible to modify the URL to default to a specific page. In this case, we are showing the Explore page with a default filter applied. Author Name = Alexy Milovidov.
          <br><br> Use the Buttons to navigate to different views. 
        </div>
  </div>
  