<script>
    import Navbar from '$lib/Navbar.svelte';
    import TopBanner from '$lib/TopBanner.svelte';

      // Import global styles
    import '../app.css';
  </script>
  
  <div class="layout">
    <TopBanner />
    <div class="content">
      <Navbar />
      <main>
        <slot />
      </main>
    </div>
  </div>
  
  <style>
    /* Layout wrapper: column layout for top banner and main content */
    .layout {
      display: flex;
      flex-direction: column;
      height: 100vh; /* Full viewport height */
      overflow: hidden;
    }
  
    /* Content wrapper: row layout for sidebar and main content */
    .content {
      display: flex;
      flex: 1;
      overflow: hidden;
    }
  
    main {
      flex: 1; /* Take the remaining space */
      padding: 1rem;
      overflow-y: auto; /* Scrollable content */
      background-color: #fff; /* White background */
    }


  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

  </style>
  