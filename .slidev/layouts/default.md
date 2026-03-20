i---
# .slidev/layouts/default.md
---

<!-- Your slide content will be inserted here -->
<slot />

<!-- Footer with CC-BY license -->
<div class="footer">
  <p>
    © {{ new Date().getFullYear() }} Julien LE SOMMER |
    <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">CC-BY 4.0</a>
    <img src="https://i.creativecommons.org/l/by/4.0/80x15.png" alt="CC-BY License" style="vertical-align:middle; margin-left:5px;" />
  </p>
</div>

<style>
  .footer {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    text-align: center;
    font-size: 0.7em;
    color: #888;
    padding: 10px;
    background: rgba(255, 255, 255, 0.7);
  }
  .footer a {
    color: #888;
    text-decoration: none;
  }
</style>

