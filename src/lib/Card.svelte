<script lang="ts">
  let waifu = $props();
</script>

<div class="cell">
  <div class="card">
    <div class="card-image">
      <figure class="image is-4by3">
        <img src={waifu.image} alt={waifu.name} class="is-fullwidth" />
      </figure>
    </div>
    <div class="card-content">
      <div class="media">
        <div class="media-content">
          <p class="title is-4">{waifu.name}</p>
          <p class="subtitle is-6">{waifu.series}</p>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .card-image .image img {
    object-fit: cover;
    width: 100%;
    height: 100%;
  }
</style>
