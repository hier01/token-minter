<!-- Page that dynamically renders each step of the Collection Generation process -->
<script>
  import { user } from "../flow/stores.js";
  import { Section, Container, FlowConnect, Stack, TransparentCard } from "$lib/components/atoms/index";
  import CollectionInfo from '$lib/components/sections/generator/CollectionInfo.svelte';
  import ContractInfo from '$lib/components/sections/generator/ContractInfo.svelte';
  import UploadAssets from '$lib/components/sections/generator/UploadAssets.svelte';
  import CollectionPreview from '$lib/components/sections/generator/CollectionPreview.svelte';
  import GeneratorNav from '$lib/components/sections/generator/GeneratorNav.svelte';
  import Deploy from '$lib/components/sections/generator/Deploy.svelte';
  import UploadMetadata from '$lib/components/sections/generator/UploadMetadata.svelte';
  import { uploadToIPFS } from "$lib/utilities/uploadToIPFS"
  import { userIPFSToken } from "$lib/stores/generator/IPFStokenStore"
  import { csvParsedFile } from "$lib/stores/generator/CsvStore"
  import { imagesFiles } from "$lib/stores/generator/ImagesStore"
  import { step, onBack, onNext } from "$lib/stores/generator/GeneratorGeneralStore"

  function uploadAssets () {
    // TODO: Upload assets to IPFS
    console.log("Uploading assets to IPFS");
    uploadToIPFS($csvParsedFile, $imagesFiles, $userIPFSToken)
    onNext();
  }
  
  const steps = [
    {
      title: "Collection Information",
      component: CollectionInfo,
      emoji: "ℹ️",
      instructions: "Define some general information around your collection.",
      onSubmitAction: onNext,
      onSubmitText: "Next",
    }, 
    {
      title: "Upload Assets",
      component: UploadAssets,
      emoji: "🗂",
      instructions: "Upload a folder with your collection. Folder must includ a file namde ....csv with your collection metadata and a folder named Images with your collection images.",
      onSubmitAction: uploadAssets,
      onSubmitText: "Upload to IPFS",
    }, 
    {
      title: "Collection Preview",
      component: CollectionPreview,
      emoji: "🖼",
      instructions: "Looks like everything is in order. Let's see what you've got.",
      onSubmitAction: onNext,
      onSubmitText: "Next",
    },
    {
      title: "Contract Information",
      component: ContractInfo,
      emoji: "📜",
      instructions: "Define some general information around your contract.",
      onSubmitAction: onNext,
      onSubmitText: "Next",
    }, 
    {
      title: "Deploy",
      component: Deploy,
      emoji: "🚀",
      instructions: "Deploy your contract to the blockchain.",
      onSubmitAction: onNext,
      onSubmitText: "Deploy",
    },
    {
      title: "Upoad Metadata",
      component: UploadMetadata,
      emoji: "👆",
      instructions: "Upload your metadata to your contract.",
      onSubmitAction: onNext,
      onSubmitText: "Deploy",
    }
  ];
</script>

<Section class="padding-top-none padding-bottom-none">
  <div class="main-wrapper">

    <!-- Display generator if user has loggedIn with wallet -->
    {#if $user?.loggedIn}
      <Container class="width-large gutter-y-none" height="100%">
        <div class="main-layout">
          <div class="sidebar-container">
            <GeneratorNav bind:step={$step} steps={steps}/>
          </div>
          <div class="main-container">
            <TransparentCard padding="2.5rem" height="100%">
              <svelte:component
                this={steps[$step].component}
                onSubmitAction={steps[$step].onSubmitAction}
                onSubmitText={steps[$step].onSubmitText}
              />
            </TransparentCard>
            </div>
        </div>
      </Container>

    <!-- If not connected, ask to connect wallet -->
    {:else}
      <div class="connection-wrapper">
        <Container>
          <TransparentCard accent={true} padding="4rem">
            <Stack>
              <p>Connect your Flow wallet to generate your collection</p>
              <FlowConnect/>
            </Stack>
          </TransparentCard>
        </Container>
      </div>
    {/if}

  </div>
</Section>

<style type="scss">
  @use "../lib/styles/abstracts" as *;
  
  .main-wrapper {
    height: 80vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 1rem;
  }
  
  .main-layout {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;

    @include mq(medium) {
      display: grid; 
      grid-template-columns: 270px 1fr;
      gap: 2rem;
      margin-bottom: 1rem;
      height: 100%;
      grid-template-areas: 
      "sidebar main";
    }
    
    .sidebar-container { 
      height: 100%;
      
      @include mq(medium) {
        grid-area: sidebar;
      }
    }
    
    .main-container { 
      grid-area: main;
      max-height: 100%;
    }
  }

  .connection-wrapper {
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    p {
      text-align: center;
    }
  }
</style>