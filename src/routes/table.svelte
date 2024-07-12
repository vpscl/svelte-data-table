<script>
  import * as Table from "$lib/components/ui/table";
  export let users = [];
</script>

<div>
  <Table.Root>
    <Table.Header class="bg-slate-50">
      <Table.Row>
        <Table.Head class="w-[100px]">Invoice</Table.Head>
        <Table.Head>Status</Table.Head>
        <Table.Head>Method</Table.Head>
        <Table.Head class="text-right">Amount</Table.Head>
      </Table.Row>
    </Table.Header>
    <Table.Body>
      {#each users as user}
        <Table.Row>
          <Table.Cell class="font-medium">{user.name.last}</Table.Cell>
          <Table.Cell>{user.email}</Table.Cell>
          <Table.Cell>{user.phone}</Table.Cell>
          <Table.Cell class="text-right">{user.location.country}</Table.Cell>
        </Table.Row>
      {/each}
    </Table.Body>
  </Table.Root>
</div>
