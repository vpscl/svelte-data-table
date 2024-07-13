<script>
  import * as DropdownMenu from "$lib/components/ui/dropdown-menu";
  import * as Pagination from "$lib/components/ui/pagination";
  import * as Table from "$lib/components/ui/table";
  import { Checkbox } from "$lib/components/ui/checkbox";
  import { Button } from "$lib/components/ui/button";
  import { Input } from "$lib/components/ui/input";
  import { Skeleton } from "$lib/components/ui/skeleton";
  import { Search, ListFilter, ArrowDownUp, ArrowUpDown } from "lucide-svelte";

  // Props
  let {
    data,
    title,
    fields,
    perPage,
    isLoading,
    error,
    searchPlaceholder = "Search",
    filterBy = fields
      .filter((field) => field.filterable)
      .map((field) => field.key)[0],
  } = $props();

  // State variables
  let searchQuery = $state("");
  let selectedItems = $state([]);
  let sortBy = $state("");
  let sortOrder = $state("asc");
  let currentPage = $state(1);

  // Derived states
  let filteredData = $derived.by(() => {
    let filtered = data.slice();

    if (searchQuery) {
      filtered = filtered.filter((item) =>
        item[filterBy].toLowerCase().includes(searchQuery.toLowerCase())
      );
      currentPage = 1;
    }

    if (sortBy) {
      filtered = filtered.sort((a, b) => {
        if (a[sortBy] === b[sortBy]) return 0;
        return a[sortBy] < b[sortBy] === (sortOrder === "asc") ? -1 : 1;
      });
    }
    return filtered;
  });

  let paginatedData = $derived.by(() => {
    const lastItemIndex = currentPage * perPage;
    const firstItemIndex = lastItemIndex - perPage;
    return filteredData.slice(firstItemIndex, lastItemIndex);
  });

  let isMasterChecked = $derived.by(() => {
    return (
      filteredData.length > 0 &&
      filteredData.every((item) => selectedItems.includes(item))
    );
  });

  // Event handlers
  const handlePageChange = (page) => {
    const totalPages = Math.ceil(filteredData.length / perPage);
    if (page < 1) {
      currentPage = 1;
    } else if (page > totalPages) {
      currentPage = totalPages;
    } else {
      currentPage = page;
    }
  };

  const handleCheckboxChange = (item, isChecked) => {
    selectedItems = isChecked
      ? [...selectedItems, item]
      : selectedItems.filter((selectedItem) => selectedItem !== item);
  };

  const handleMasterCheckboxChange = (isChecked) => {
    selectedItems = isChecked
      ? selectedItems.concat(
          filteredData.filter((item) => !selectedItems.includes(item))
        )
      : selectedItems.filter((item) => !filteredData.includes(item));
  };

  const handleSortChange = (column) => {
    if (sortBy === column) {
      sortOrder = sortOrder === "asc" ? "desc" : "asc";
    } else {
      sortBy = column;
      sortOrder = "asc";
    }
    currentPage = 1;
  };
</script>

<div class="flex flex-col space-y-4">
  <div class="rounded-lg border">
    <div class="flex flex-col p-4 border-b md:justify-between md:flex-row">
      <!-- Header section -->
      <h1 class="flex items-center mb-3 text-lg font-semibold md:mb-0">
        {title}
      </h1>
      <div class="flex flex-col md:space-x-3 md:flex-row">
        <!-- Search and filter section -->
        <div class="relative mb-2 md:mb-0">
          <Search class="absolute inset-y-0 left-4 mr-2 w-4 h-full" />
          <Input
            placeholder={searchPlaceholder}
            type="text"
            class="pl-10 w-full md:w-72"
            bind:value={searchQuery}
          />
        </div>
        <DropdownMenu.Root>
          <DropdownMenu.Trigger asChild let:builder>
            <Button class="mb-1 md:mb-0" variant="outline" builders={[builder]}>
              <ListFilter class="mr-2 w-4 h-4" />
              Filters
            </Button>
          </DropdownMenu.Trigger>
          <DropdownMenu.Content class="w-56">
            <DropdownMenu.RadioGroup bind:value={filterBy}>
              {#each fields as field}
                {#if field.filterable}
                  <DropdownMenu.RadioItem value={field.key}
                    >{field.label}</DropdownMenu.RadioItem
                  >
                {/if}
              {/each}
            </DropdownMenu.RadioGroup>
          </DropdownMenu.Content>
        </DropdownMenu.Root>
      </div>
    </div>

    <!-- Table section -->
    <Table.Root>
      <Table.Header class="bg-slate-50">
        <Table.Row>
          {#if !(error || isLoading || paginatedData.length === 0)}
            <Table.Head>
              <Checkbox
                checked={isMasterChecked}
                onCheckedChange={handleMasterCheckboxChange}
              />
            </Table.Head>
          {:else}
            <Table.Head class="w-5"></Table.Head>
          {/if}
          <!-- Column headers -->
          {#each fields as field}
            {#if field.sortable}
              <Table.Head>
                <button
                  class="flex items-center cursor-pointer"
                  onclick={() => handleSortChange(field.key)}
                >
                  {field.label}
                  {#if sortBy === field.key}
                    {#if sortOrder === "asc"}
                      <ArrowDownUp class="ml-2 text-slate-800" size={12} />
                    {:else}
                      <ArrowUpDown class="ml-2 text-slate-800" size={12} />
                    {/if}
                  {:else}
                    <ArrowDownUp class="ml-2 text-slate-400" size={12} />
                  {/if}
                </button>
              </Table.Head>
            {:else}
              <Table.Head>{field.label}</Table.Head>
            {/if}
          {/each}
        </Table.Row>
      </Table.Header>
      <Table.Body class="relative">
        {#if error}
          <Table.Row class="relative h-20">
            <Table.Cell>
              <p
                class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2"
              >
                {error}
              </p>
            </Table.Cell>
          </Table.Row>
        {:else if isLoading}
          {#each new Array(perPage) as item}
            <Table.Row class="relative">
              <Table.Cell>
                <Skeleton class="w-5 h-5" />
              </Table.Cell>
              {#each fields as field}
                <Table.Cell>
                  <Skeleton class="w-full h-5" />
                </Table.Cell>
              {/each}
            </Table.Row>
          {/each}
        {:else if paginatedData.length === 0 && !isLoading}
          <Table.Row class="relative h-20">
            <Table.Cell>
              <p
                class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2"
              >
                No data found
              </p>
            </Table.Cell>
          </Table.Row>
        {:else}
          <!-- Table rows -->
          {#each paginatedData as item}
            <Table.Row>
              <Table.Cell>
                <Checkbox
                  checked={selectedItems.includes(item)}
                  onCheckedChange={(event) => handleCheckboxChange(item, event)}
                />
              </Table.Cell>
              {#each fields as field}
                <Table.Cell class="whitespace-nowrap"
                  >{item[field.key]}</Table.Cell
                >
              {/each}
            </Table.Row>
          {/each}
        {/if}
      </Table.Body>
    </Table.Root>
  </div>

  <!-- Pagination section -->
  <Pagination.Root
    count={filteredData.length}
    {perPage}
    {currentPage}
    let:pages
  >
    <Pagination.Content>
      <Pagination.Item>
        <Pagination.PrevButton
          disabled={currentPage === 1}
          onclick={() => {
            handlePageChange(currentPage - 1);
          }}
        />
      </Pagination.Item>
      <div class="hidden items-center md:flex">
        {#each pages as page (page.key)}
          {#if page.type === "ellipsis"}
            <Pagination.Item>
              <Pagination.Ellipsis />
            </Pagination.Item>
          {:else}
            <Pagination.Item isVisible={currentPage == page.value}>
              <Pagination.Link
                {page}
                isActive={currentPage == page.value}
                onclick={() => handlePageChange(page.value)}
              >
                {page.value}
              </Pagination.Link>
            </Pagination.Item>
          {/if}
        {/each}
      </div>
      <Pagination.Item>
        <Pagination.NextButton
          disabled={currentPage === Math.ceil(filteredData.length / perPage)}
          onclick={() => {
            handlePageChange(currentPage + 1);
          }}
        />
      </Pagination.Item>
    </Pagination.Content>
  </Pagination.Root>
</div>
