<script>
    import { onMount } from 'svelte';
  
    // State for loading, iframe URL, and errors
    let isLoading = true;
    let iframeSrc = '';
    let error = '';
  
    // Fetch the iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/custom-attribute-iframe', {
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
        Similar to `Row Access policy Embed Dashboard`, we can also pass custom attributes via the embed URL creation process. In this case, you will need to use a JSON object called attributes to pass these into Rill. Similarly, Rill at the metrics view will be able to filter to dashboard based on your defined row access policy .
        <div>
            <h3> URL embed Contents </h3>
            <pre
              style={{
                backgroundColor: '#f4f4f4',
                padding: '10px',
                borderRadius: '5px',
                overflowX: 'auto',
                textAlign: 'left',
                margin: '20px 0',
              }}>
              <code>
                {`body: JSON.stringify({
                  resource: rillDashboard,
                  attributes: {
                      "custom_attribute_from_embed": "Value2",
                      "embed_pub_name": "LG USA"
                  }
                  // You can pass additional parameters for row-level security policies here.
                  // For details, see: https://docs.rilldata.com/integrate/embedding
              }),`}
              </code>
            </pre>
  
            <h3>Rill Metrics View</h3>
  
            <pre
              style={{
                backgroundColor: '#f4f4f4',
                padding: '10px',
                borderRadius: '5px',
                overflowX: 'auto',
                textAlign: 'left',
                margin: '20px 0',
              }}
            >
              <code>
{`security:
    access: true
    row_filter: "Pub_Name = '{{ .user.embed_pub_name }}'"
    #row_filter: "Pub_Name IN (SELECT PubName FROM test WHERE custom_attribute = '{{ .user.custom_attribute_from_embed }}')"
  
  
  `}
              </code>
            </pre>
  
      
            </div>

    </div>
</div>
