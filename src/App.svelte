<script>
  import meetups from "./MeetUps/meetups-store";
  import Header from "./UI/Header.svelte";
  import MeetupGrid from "./MeetUps/MeetupGrid.svelte";
  import Button from "./UI/Button.svelte";
  import EditMeetup from "./MeetUps/EditMeetup.svelte";
  import MeetupDetail from ".//MeetUps/MeetupDetail.svelte";

  // let meetups = ;

  let editMode = null;
  let editedId;
  let page = "overview";
  let pageData = {};

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
</script>

<Header />

<main>
  {#if page === "overview"}
    {#if editMode === "edit"}
      <EditMeetup id={editedId} on:save={savedMeetup} on:cancel={cancelEdit} />
    {/if}
    <MeetupGrid
      meetups={$meetups}
      on:showDetails={showDetails}
      on:edit={startEdit}
      on:add={() => {
        editMode = "edit";
      }}
    />
  {:else}
    <MeetupDetail id={pageData.id} on:close={closeDetails} />
  {/if}
</main>

<style>
  main {
    padding-top: 5rem;
  }

  .meetup-controls {
    margin: 1rem;
  }
</style>
