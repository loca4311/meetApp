<script>
  import { createEventDispatcher } from "svelte";

  import meetups from "./meetups-store";

  import TextInput from "../UI/TextInput.svelte";
  import Button from "../UI/Button.svelte";
  import Modal from "../UI/Modal.svelte";

  import {
    isEmpty,
    isValidEmail,
    isValidImageUrl,
  } from "../hepers/validation.js";

  export let id = null;

  let title = "";
  let subtitle = "";
  let address = "";
  let email = "";
  let description = "";
  let imageUrl = "";

  if (id) {
    const unsubscribe = meetups.subscribe((items) => {
      const selectedMeetup = items.find((i) => i.id === id);

      title = selectedMeetup.title;
      subtitle = selectedMeetup.subtitle;
      address = selectedMeetup.address;
      email = selectedMeetup.contactEmail;
      description = selectedMeetup.description;
      imageUrl = selectedMeetup.imageUrl;
    });

    unsubscribe();
  }

  const dispatch = createEventDispatcher();

  $: titleValid = !isEmpty(title);
  $: emailValid = isValidEmail(email);
  $: subtitleValid = !isEmpty(subtitle);
  $: addressValid = !isEmpty(address);
  $: descriptionValid = !isEmpty(description);
  $: imageUrlValid = isValidImageUrl(imageUrl);
  $: formIsValid =
    titleValid &&
    emailValid &&
    subtitleValid &&
    addressValid &&
    descriptionValid &&
    imageUrlValid;

  function submitForm() {
    const meetupData = {
      title,
      subtitle,
      description,
      imageUrl,
      address,
      contactEmail: email,
    };

    if (id) {
      meetups.updateMeetup(id, meetupData);
    } else {
      meetups.addMeetup(meetupData);
    }

    dispatch("save");
  }

  function deleteMeetup() {
    meetups.removeMeetup(id);
    dispatch("save");
  }

  function cancel() {
    dispatch("cancel");
  }
</script>

<Modal title="Edit Meetup Data" on:cancel>
  <form on:submit|preventDefault={submitForm}>
    <TextInput
      id="title"
      name="title"
      valid={titleValid}
      validityMessage="Please enter a valid title."
      label="Title"
      value={title}
      on:input={(event) => (title = event.target.value)}
    />
    <TextInput
      id="subtitle"
      name="subtitle"
      label="Subtitle"
      valid={subtitleValid}
      validityMessage="Please enter a valid subtitle."
      value={subtitle}
      on:input={(event) => (subtitle = event.target.value)}
    />
    <TextInput
      id="address"
      name="address"
      label="Address"
      valid={addressValid}
      validityMessage="Please enter a valid address."
      value={address}
      on:input={(event) => (address = event.target.value)}
    />
    <TextInput
      id="imageUrl"
      name="imageUrl"
      label="Image Url"
      valid={imageUrlValid}
      validityMessage="Please enter a valid image url."
      value={imageUrl}
      on:input={(event) => (imageUrl = event.target.value)}
    />
    <TextInput
      id="email"
      name="email"
      label="E-mail"
      type="email"
      valid={emailValid}
      validityMessage="Please enter a valid email."
      value={email}
      on:input={(event) => (email = event.target.value)}
    />
    <TextInput
      id="description"
      name="description"
      label="Description"
      controlType="textarea"
      valid={descriptionValid}
      validityMessage="Please enter a valid description."
      rows="3"
      bind:value={description}
    />
  </form>
  <div slot="footer">
    <Button type="button" mode="outline" on:click={cancel}>Cancel</Button>
    <Button type="button" on:click={submitForm} disabled={!formIsValid}
      >Save</Button
    >
    {#if id}
      <Button type="button" on:click={deleteMeetup}>Delete</Button>
    {/if}
  </div>
</Modal>

<style>
  form {
    width: 100%;
  }
</style>
