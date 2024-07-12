<script>
  import { cn } from "$lib/utils.js";
  let className = undefined;
  export { className as class };
</script>

<div class="relative w-full overflow-auto rounded-lg border">
  <div class="p-4 flex justify-between border-b">
    <h1 class="text-lg font-semibold">Users</h1>
    <div class="">
      <input
        class="border rounded-sm px-2 py-1 text-sm"
        placeholder="Search"
        type="text"
      />
    </div>
  </div>
  <table
    class={cn("w-full caption-bottom text-sm", className)}
    {...$$restProps}
  >
    <slot />
  </table>
</div>
