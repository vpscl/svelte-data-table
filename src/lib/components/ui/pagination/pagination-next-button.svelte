<script>
  import { Pagination as PaginationPrimitive } from "bits-ui";
  import ChevronRight from "lucide-svelte/icons/chevron-right";
  import { Button } from "$lib/components/ui/button/index.js";
  import { cn } from "$lib/utils.js";
  let className = undefined;
  export { className as class };
</script>

<PaginationPrimitive.NextButton asChild let:builder>
  <Button
    variant="ghost"
    class={cn("gap-1 pr-2.5", className)}
    builders={[builder]}
    on:click
    {...$$restProps}
  >
    <slot>
      <span>Next</span>
      <ChevronRight class="w-4 h-4" />
    </slot>
  </Button>
</PaginationPrimitive.NextButton>
