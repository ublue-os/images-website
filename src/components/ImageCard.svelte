<script lang="ts">
  import { onMount } from "svelte";
  import { getGithubRepository } from "@ts/apiHelpers";
  import { templateStr } from "@ts/templateStrings";
  import type { Image, ImageFilter } from "@ts/types";

  import Icon from "@iconify/svelte";
  import gitFork from "@iconify/icons-ph/git-fork-bold";
  import starRounded from "@iconify/icons-material-symbols/star-rounded";
  import RebaseCommand from "@components/RebaseCommand.svelte";
  import Box from "@components/Box.svelte";
  import IconList from "@components/IconList.svelte";

  export let image: Image;
  export let filter: ImageFilter;

  $: filteredEditions = image.editions.filter((e) => {
    if (filter.featureSet.length < 1 && filter.desktop === null) return true;

    if (filter.desktop !== null && filter.desktop.length > 0) {
      return filter.desktop === e.desktop;
    }
    return true;
  });

  let githubRepo: Object | undefined;

  onMount(async () => {
    githubRepo = await getGithubRepository(image.githubRepo);
    if (githubRepo["html_url"] == undefined) githubRepo = undefined;
  });
</script>

{#each filteredEditions as edition}
  <div
    class="flex flex-col border-4 border-solid border-indigo-800 p-4 dark:border-indigo-700 dark:text-white"
  >
    <div class="flex flex-row items-center gap-2">
      <h2 class="mr-8 text-xl font-bold text-indigo-800 dark:text-indigo-600">
        {templateStr(image.name, { edition: edition })}
      </h2>
      {#if githubRepo}
        <Box class="ml-auto px-2 py-[0.2rem]">
          <a
            href={githubRepo["html_url"]}
            class="flex items-center font-bold"
            title="Open the images Github repository."
          >
            <Icon icon={gitFork} />
            {githubRepo["forks_count"]}
          </a>
        </Box>
        <Box class="px-2 py-[0.2rem]">
          <a
            href={githubRepo["html_url"]}
            class="flex items-center font-bold"
            title="Open the images Github repository."
          >
            <Icon icon={starRounded} />
            {githubRepo["stargazers_count"]}
          </a>
        </Box>
      {/if}
    </div>
    <span>{image.creator}</span>
    <IconList {image} {edition} />
    <Box
      class="ml-2 mt-3 mb-4 max-w-xl outline-gray-100 hover:outline-gray-100"
    >
      {edition.description}
    </Box>
    <RebaseCommand {image} {edition} />
  </div>
{/each}
