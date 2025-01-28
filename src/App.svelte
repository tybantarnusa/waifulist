<script lang="ts">
  import "bulma/css/bulma.min.css";
  import Card from "./lib/Card.svelte";
  import Header from "./lib/Header.svelte";
  import { onMount } from "svelte";
  import * as d3 from "d3";
  import createFuzzySearch, { type FuzzySearcher } from "@nozbe/microfuzz";

  interface Waifu {
    name: string;
    series: string;
    image: string;
  }

  let search: string = $state("");

  let allWaifus: Waifu[] = $state([]);
  let filteredWaifus: Waifu[] = $derived.by(doSearch);

  const initialSize = 9;
  let size: number = $state(initialSize);
  let waifus: Waifu[] = $derived.by(getWaifusAtSize);

  let fuzzySearch: FuzzySearcher<Waifu> | null = null;

  onMount(async () => {
    allWaifus = (await d3.csv("./waifus.csv")) as unknown as Waifu[];
    allWaifus.sort(() => Math.random() - 0.5);

    fuzzySearch = createFuzzySearch(allWaifus, {
      getText: (waifu) => [waifu.name, waifu.series],
    });
  });

  function doSearch(): Waifu[] {
    if (!search) return allWaifus;
    if (!fuzzySearch) return allWaifus;

    const result = fuzzySearch(search).map((r) => r.item);
    if (result.length === 0) {
      return allWaifus;
    }

    return result;
  }

  function getWaifusAtSize(): Waifu[] {
    return filteredWaifus.slice(0, size);
  }
</script>

<main class="has-background-black-ter">
  <Header />
  <div class="container columns">
    <div class="column">
      <div class="field">
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Search by name or series..."
            bind:value={search}
          />
        </div>
      </div>

      <div class="fixed-grid has-3-cols-desktop has-1-cols-mobile">
        <div class="grid">
          {#each waifus as waifu}
            <Card {...waifu} />
          {/each}
        </div>
      </div>

      {#if waifus.length < filteredWaifus.length}
        <button
          class="button is-white is-outlined is-fullwidth"
          onclick={() => (size += 9)}>Load more</button
        >
      {/if}
    </div>
  </div>
</main>
