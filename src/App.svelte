<script>
  import meetups from "./MeetUps/meetups-store";
  import Header from "./UI/Header.svelte";
  import MeetupGrid from "./MeetUps/MeetupGrid.svelte";
  import Button from "./UI/Button.svelte";
  import EditMeetup from "./MeetUps/EditMeetup.svelte";
  import MeetupDetail from ".//MeetUps/MeetupDetail.svelte";
  import LoadingSpinner from "./UI/LoadingSpinner.svelte";
  import Error from "./UI/Error.svelte";

  // let meetups = ;

  let editMode = null;
  let editedId;
  let page = "overview";
  let pageData = {};
  let isLoading = true;
  let error;

  fetch(
    "https://meetup-svelte-4c3e7-default-rtdb.europe-west1.firebasedatabase.app/meetups.json"
  )
    .then((res) => {
      if (!res.ok) {
        throw new Error("Fetching meetups failde, please try again later!");
      }

      return res.json();
    })
    .then((data) => {
      const loadedMeetups = [];
      for (const key in data) {
        loadedMeetups.push({
          ...data[key],
          id: key,
        });
      }
      setTimeout(() => {
        isLoading = false;
        meetups.setMeetups(loadedMeetups.reverse());
      }, 1000);
    })
    .catch((err) => {
      error = err;
      isLoading = false;
      console.log(error);
    });

  function savedMeetup(event) {
    editMode = null;
    editedId = null;
  }

  function cancelEdit() {
    editMode = null;
    editedId = null;
  }

  function showDetails(event) {
    page = "details";
    pageData.id = event.detail;
  }

  function closeDetails() {
    page = "overview";
    pageData = {};
  }

  function startEdit(event) {
    editMode = "edit";
    editedId = event.detail;
  }

  function clearError() {
    error = null;
  }
</script>

<Header />

<main>
  {#if page === "overview"}
    {#if editMode === "edit"}
      <EditMeetup id={editedId} on:save={savedMeetup} on:cancel={cancelEdit} />
    {/if}
    {#if isLoading}
      <LoadingSpinner />
    {:else}
      <MeetupGrid
        meetups={$meetups}
        on:showDetails={showDetails}
        on:edit={startEdit}
        on:add={() => {
          editMode = "edit";
        }}
      />
    {/if}
  {:else}
    <MeetupDetail id={pageData.id} on:close={closeDetails} />
  {/if}
</main>

{#if error}
  <Error message={error.message} on:cancel={clearError} />
{/if}

<style>
  main {
    padding-top: 5rem;
  }

  .meetup-controls {
    margin: 1rem;
  }
</style>
