<script>
  import DataTable from "$lib/components/ui/data-table";

  // State variables
  let fields = $state([
    { key: "name", label: "Name", sortable: true, filterable: true },
    { key: "username", label: "Username", sortable: true },
    { key: "email", label: "Email", sortable: true },
    { key: "phone", label: "Phone" },
    { key: "city", label: "City", filterable: true },
    { key: "country", label: "Country", filterable: true },
  ]);

  let users = $state([]);
  let error = $state();
  let isLoading = $state(false);
  let perPage = 7;

  const fetchData = async () => {
    isLoading = true;

    try {
      const response = await fetch("https://randomuser.me/api/?results=100");
      const { results } = await response.json();
      users = results.map((user) => ({
        name: `${user.name.first} ${user.name.last}`,
        username: user.login.username,
        email: user.email,
        phone: user.phone,
        city: user.location.city,
        country: user.location.country,
      }));
    } catch (err) {
      error = "Failed to fetch data";
    }

    isLoading = false;
  };

  $effect(() => {
    fetchData();
  });
</script>

<div class="container py-10 mx-auto">
  <DataTable
    data={users}
    title="Users"
    {fields}
    {perPage}
    {isLoading}
    {error}
  />
</div>
