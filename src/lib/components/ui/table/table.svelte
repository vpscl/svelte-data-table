<script>
  import { cn } from "$lib/utils.js";
  let className = undefined;
  export { className as class };
</script>

<div class="overflow-auto relative w-full">
  <table
    class={cn("w-full caption-bottom text-sm", className)}
    {...$$restProps}
  >
    <slot />
  </table>
</div>
