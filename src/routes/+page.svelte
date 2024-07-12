<script>
  import Table from "./table.svelte";
  import { onMount } from "svelte";

  let users = [];

  onMount(async () => {
    const response = await fetch("https://randomuser.me/api/?results=5");
    const data = await response.json();
    users = data.results;
  });
</script>

<div class="container mx-auto py-10">
  <Table {users} />
</div>
