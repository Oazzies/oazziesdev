<script lang="ts">
  import PixelTransition from "$lib/components/PixelTransition.svelte";
</script>

<PixelTransition gridSize={12} pixelColor="#171717" animationStepDuration={0.4} class="h-screen w-screen">
  {#snippet firstContent()}
    <div class="flex size-full items-center justify-center">
      <img src="/avatarHead.png" alt="oml its me" class="h-auto w-44 object-cover" />
    </div>
  {/snippet}
  {#snippet secondContent()}
    <div class="relative size-full">
      <div class="absolute top-0 z-[-2] h-screen w-screen bg-neutral-950 bg-[radial-gradient(ellipse_80%_80%_at_50%_-20%,rgba(120,119,198,0.3),rgba(255,255,255,0))]"></div>
      <div class="grid size-full place-items-center">
        <h1 class="font-mono text-7xl font-black text-neutral-50">Oazzies</h1>
      </div>
    </div>
  {/snippet}
</PixelTransition>

<div class="absolute top-0 z-[-2] h-screen w-screen bg-neutral-950 bg-[radial-gradient(ellipse_80%_80%_at_50%_-20%,rgba(120,119,198,0.3),rgba(255,255,255,0))]"></div>
