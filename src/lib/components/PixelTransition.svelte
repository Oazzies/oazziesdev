<script lang="ts">
  import { IsHover } from "$lib/hooks/is-hover.svelte";
  import { gsap } from "gsap";
  import type { Snippet } from "svelte";

  interface PixelTransitionProps {
    firstContent: Snippet;
    secondContent: Snippet;
    gridSize?: number;
    pixelColor?: string;
    animationStepDuration?: number;
    class?: string;
    style?: string;
    aspectRatio?: string;
  }

  let { firstContent, secondContent, gridSize = 16, pixelColor = "currentColor", animationStepDuration = 0.3, class: className = "", style = "", aspectRatio = "100%" }: PixelTransitionProps = $props();

  let containerRef = $state<HTMLDivElement | null>(null);
  let pixelGridRef = $state<HTMLDivElement | null>(null);
  let activeRef = $state<HTMLDivElement | null>(null);
  let delayedCallRef = $state<gsap.core.Tween | null>(null);

  let isActive = $state<boolean>(false);

  let isTouchDevice = $state(new IsHover());

  $effect(() => {
    const pixelGridEl = pixelGridRef;
    if (!pixelGridEl) return;

    pixelGridEl.innerHTML = "";

    for (let row = 0; row < gridSize; row++) {
      for (let col = 0; col < gridSize; col++) {
        const pixel = document.createElement("div");
        pixel.classList.add("pixelated-image-card__pixel");
        pixel.classList.add("absolute", "hidden");
        pixel.style.backgroundColor = pixelColor;

        const size = 100 / gridSize;
        pixel.style.width = `${size}%`;
        pixel.style.height = `${size}%`;
        pixel.style.left = `${col * size}%`;
        pixel.style.top = `${row * size}%`;

        pixelGridEl.appendChild(pixel);
      }
    }
  });

  const animatePixels = (activate: boolean): void => {
    // setIsActive(activate);

    const pixelGridEl = pixelGridRef;
    const activeEl = activeRef;
    if (!pixelGridEl || !activeEl) return;

    const pixels = pixelGridEl.querySelectorAll<HTMLDivElement>(".pixelated-image-card__pixel");
    if (!pixels.length) return;

    gsap.killTweensOf(pixels);
    if (delayedCallRef) {
      delayedCallRef.kill();
    }

    gsap.set(pixels, { display: "none" });

    const totalPixels = pixels.length;
    const staggerDuration = animationStepDuration / totalPixels;

    gsap.to(pixels, {
      display: "block",
      duration: 0,
      stagger: {
        each: staggerDuration,
        from: "random"
      }
    });

    delayedCallRef = gsap.delayedCall(animationStepDuration, () => {
      activeEl.style.display = activate ? "block" : "none";
      activeEl.style.pointerEvents = activate ? "none" : "";
    });

    gsap.to(pixels, {
      display: "none",
      duration: 0,
      delay: animationStepDuration,
      stagger: {
        each: staggerDuration,
        from: "random"
      }
    });
  };

  const handleMouseEnter = (): void => {
    if (!isActive) animatePixels(true);
  };
  const handleMouseLeave = (): void => {
    if (isActive) animatePixels(false);
  };
  const handleClick = (): void => {
    animatePixels(!isActive);
  };
</script>

<div role="none" bind:this={containerRef} class="{className} relative max-w-full overflow-hidden text-white" {style} onmouseenter={!isTouchDevice.current ? handleMouseEnter : undefined} onmouseleave={!isTouchDevice.current ? handleMouseLeave : undefined} onclick={isTouchDevice.current ? handleClick : undefined}>
  <div style="padding-top: {aspectRatio}"></div>

  <div class="absolute inset-0 h-full w-full">{@render firstContent?.()}</div>

  <div bind:this={activeRef} class="absolute inset-0 z-[2] h-full w-full" style="display: none">
    {@render secondContent?.()}
  </div>

  <div bind:this={pixelGridRef} class="pointer-events-none absolute inset-0 z-[3] h-full w-full"></div>
</div>
