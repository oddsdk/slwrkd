<script lang="ts">
  import { page } from '$app/stores'

  import { fetchAllAccounts, fetchGameState, fetchTopStreaks } from '$lib/contract'
  import { contractStore, networkStore } from '$src/stores'
  import Countdown from '$components/common/Countdown.svelte'
  import InContextLoader from '$components/common/InContextLoader.svelte'
  import InfoCarousel from '$components/play/InfoCarousel.svelte'
  import MoveHistory from '$components/play/MoveHistory.svelte'
  import YourMoveVsPreviousMove from '$components/play/YourMoveVsPreviousMove.svelte'

  if (!$contractStore?.results?.length) {
    fetchGameState()
  }

  let topStreak = 0
  if (!$contractStore?.topStreaks?.length) {
    fetchTopStreaks().then(() => {
      topStreak = $contractStore.topStreaks[0]?.length
    })
  } else {
    topStreak = $contractStore.topStreaks[0]?.length
  }

  let moveHistoryLoading = true
  const moveHistoryLoadingComplete = () => moveHistoryLoading = false

  $: previousMove = $contractStore?.previousWinner?.result
  $: {
    if (!$contractStore?.allAccounts?.length) {
      fetchTopStreaks().then(() => {
        topStreak = $contractStore.topStreaks[0]?.length
      })
    } else {
      topStreak = $contractStore.topStreaks[0]?.length
    }
  }
</script>

<div class="pt-7 pb-2">
  {#if $contractStore?.results?.length && previousMove}
    <!-- Header scoreboard -->
    <div class="fixed z-10 top-0 left-0 right-0 px-10 py-6 flex items-center justify-between bg-base-content text-base-100 font-bold text-base">
      <div class="flex items-center gap-2">
        <img src="{window.location.origin}/chain.svg" class="h-[15px] w-auto" alt="streak" />
        <p>STREAK: {$contractStore?.networkStreak}</p>
      </div>
      <div class="flex items-center gap-2">
        <img src="{window.location.origin}/leaderboard.svg" class="h-[15px] w-auto" alt="trophy" />
        <p>{topStreak}</p>
      </div>
    </div>
    <div class="faded-overlay fixed z-10 top-[67px] left-0 right-0" />

    <!-- Move history -->
    <MoveHistory loadingComplete={moveHistoryLoadingComplete} />

    <!-- Your move vs the previous move -->
    <YourMoveVsPreviousMove />

    <!-- Carousel -->
    <div class="px-10">
      <InfoCarousel />
    </div>
  {:else}
    <div class="px-10">
      <InContextLoader heightClass="h-[calc(100vh-250px)]" />
    </div>
  {/if}

  <div class="px-10">
    <!-- {#if !!$networkStore.pendingTransaction}
      <button disabled={true} class="btn btn-primary btn-lg w-full justify-between text-lg uppercase rounded-none">
        <span class="btn-loading">Counting Votes</span> <img src={`${window.location.origin}/clock.svg`} class="" alt="counting votes" />
      </button>
      <div class="hidden"><Countdown /></div>
    {:else} -->
      <a href="/{$page.params.team}/vote" class="btn btn-primary btn-lg w-full justify-between text-lg uppercase rounded-none">
        Cast your vote <Countdown />
      </a>
    <!-- {/if} -->
  </div>
</div>
