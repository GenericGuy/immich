<script lang="ts">
  import { locale } from '$lib/stores/preferences.store';
  import type { ServerStatsResponseDto } from '@api';
  import CameraIris from 'svelte-material-icons/CameraIris.svelte';
  import Memory from 'svelte-material-icons/Memory.svelte';
  import PlayCircle from 'svelte-material-icons/PlayCircle.svelte';
  import { asByteUnitString, getBytesWithUnit } from '../../../utils/byte-units';
  import StatsCard from './stats-card.svelte';

  export let stats: ServerStatsResponseDto = {
    photos: 0,
    videos: 0,
    usage: 0,
    usageByUser: [],
  };

  $: zeros = (value: number) => {
    const maxLength = 13;
    const valueLength = value.toString().length;
    const zeroLength = maxLength - valueLength;

    return '0'.repeat(zeroLength);
  };

  $: [statsUsage, statsUsageUnit] = getBytesWithUnit(stats.usage, 0);
</script>

<div class="flex flex-col gap-5">
  <div>
    <p class="text-sm dark:text-immich-dark-fg">TOTAL USAGE</p>

    <div class="mt-5 hidden justify-between lg:flex">
      <StatsCard logo={CameraIris} title="PHOTOS" value={stats.photos} />
      <StatsCard logo={PlayCircle} title="VIDEOS" value={stats.videos} />
      <StatsCard logo={Memory} title="STORAGE" value={statsUsage} unit={statsUsageUnit} />
    </div>
    <div class="mt-5 flex lg:hidden">
      <div class="flex flex-col justify-between rounded-3xl bg-immich-gray p-5 dark:bg-immich-dark-gray">
        <div class="flex flex-wrap gap-x-12">
          <div class="flex place-items-center gap-4 text-immich-primary dark:text-immich-dark-primary">
            <CameraIris size="25" />
            <p>PHOTOS</p>
          </div>

          <div class="relative text-center font-mono text-2xl font-semibold">
            <span class="text-[#DCDADA] dark:text-[#525252]">{zeros(stats.photos)}</span><span
              class="text-immich-primary dark:text-immich-dark-primary">{stats.photos}</span
            >
          </div>
        </div>
        <div class="flex flex-wrap gap-x-12">
          <div class="flex place-items-center gap-4 text-immich-primary dark:text-immich-dark-primary">
            <PlayCircle size="25" />
            <p>VIDEOS</p>
          </div>

          <div class="relative text-center font-mono text-2xl font-semibold">
            <span class="text-[#DCDADA] dark:text-[#525252]">{zeros(stats.videos)}</span><span
              class="text-immich-primary dark:text-immich-dark-primary">{stats.videos}</span
            >
          </div>
        </div>
        <div class="flex flex-wrap gap-x-7">
          <div class="flex place-items-center gap-4 text-immich-primary dark:text-immich-dark-primary">
            <Memory size="25" />
            <p>STORAGE</p>
          </div>

          <div class="relative flex text-center font-mono text-2xl font-semibold">
            <span class="text-[#DCDADA] dark:text-[#525252]">{zeros(statsUsage)}</span><span
              class="text-immich-primary dark:text-immich-dark-primary">{statsUsage}</span
            >
            <span class="my-auto ml-2 text-center text-base font-light text-gray-400">{statsUsageUnit}</span>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div>
    <p class="text-sm dark:text-immich-dark-fg">USER USAGE DETAIL</p>
    <table class="mt-5 w-full text-left">
      <thead
        class="mb-4 flex h-12 w-full rounded-md border bg-gray-50 text-immich-primary dark:border-immich-dark-gray dark:bg-immich-dark-gray dark:text-immich-dark-primary"
      >
        <tr class="flex w-full place-items-center">
          <th class="w-1/4 text-center text-sm font-medium">User</th>
          <th class="w-1/4 text-center text-sm font-medium">Photos</th>
          <th class="w-1/4 text-center text-sm font-medium">Videos</th>
          <th class="w-1/4 text-center text-sm font-medium">Size</th>
        </tr>
      </thead>
      <tbody
        class="block max-h-[320px] w-full overflow-y-auto rounded-md border dark:border-immich-dark-gray dark:text-immich-dark-fg"
      >
        {#each stats.usageByUser as user (user.userId)}
          <tr
            class="flex h-[50px] w-full place-items-center text-center odd:bg-immich-gray even:bg-immich-bg odd:dark:bg-immich-dark-gray/75 even:dark:bg-immich-dark-gray/50"
          >
            <td class="w-1/4 text-ellipsis px-2 text-sm">{user.userFirstName} {user.userLastName}</td>
            <td class="w-1/4 text-ellipsis px-2 text-sm">{user.photos.toLocaleString($locale)}</td>
            <td class="w-1/4 text-ellipsis px-2 text-sm">{user.videos.toLocaleString($locale)}</td>
            <td class="w-1/4 text-ellipsis px-2 text-sm">{asByteUnitString(user.usage, $locale)}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>
