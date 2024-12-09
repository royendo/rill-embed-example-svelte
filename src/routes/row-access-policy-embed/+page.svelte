<script>
    import { onMount } from 'svelte';
  
    // State for loading, iframe URL, and errors
    let isLoading = true;
    let iframeSrc = '';
    let error = '';
  
    // Fetch the iframe URL on component mount
    onMount(async () => {
      try {
        const response = await fetch('/api/row-access-policy-iframe', {
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
        This is an example of a dashboard with row access policies enabled. When creating the embed URL we pass the following email `test@domain.com` which is recognized at the metrics view to filter the Pub Name dimension to Disney. Depending on your use case, you can pass `user_id`, `user_email`, `user_domain`, or attributes.

        <div>
            <h3>URL embed contents</h3>
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
 {`body: JSON.stringify({
    resource: rillDashboard,
    user_email: 'test@domain.com',
    // You can pass additional parameters for row-level security policies here.
    // For details, see: https://docs.rilldata.com/integrate/embedding
  }),`}
              </code>
            </pre>
  
            <h3>Rill Metric View Access Policy</h3>
  
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
    row_filter: "Pub_Name IN (SELECT PubName FROM mapping WHERE domain = '{{ .user.domain }}')"
  `}
              </code>
            </pre> 
        <h3> Mapping File </h3>
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
{`    SELECT * FROM (VALUES 
        ('Disney', 'domain.com')
      ) AS t(PubName, domain)`}
              </code>
            </pre>
            </div>
    </div>
</div>
