<script lang="ts">
  import download from 'downloadjs';
  import { toast } from 'svelte-sonner';
  import type { iSVG } from '../types/svg';
  import { MIMETYPE, getSvgContent } from '../utils/getSvgContent';

  // Icons:
  import DownloadSimple from 'phosphor-svelte/lib/DownloadSimple';
  import ArrowUpRight from 'phosphor-svelte/lib/ArrowUpRight';
  import Copy from 'phosphor-svelte/lib/Copy';

  // Props:
  export let svgInfo: iSVG;

  // Download SVG:
  const downloadSvg = (url?: string) => {
    download(url || '');
    toast.success('Downloading...');
  };

  // Copy SVG to clipboard:
  const copyToClipboard = async (url?: string) => {
    const data = {
      [MIMETYPE]: getSvgContent(url, true)
    };
    try {
      const clipboardItem = new ClipboardItem(data);
      await navigator.clipboard.write([clipboardItem]);
    } catch (error) {
      const content = (await getSvgContent(url, false)) as string;
      await navigator.clipboard.writeText(content);
    }
    toast.success('Copied to clipboard!', {
      description: `${svgInfo.title} - ${svgInfo.category}`
    });
  };
</script>

<div
  class="flex flex-col items-center justify-center rounded-md border border-neutral-300 bg-neutral-100 p-4 dark:border-neutral-800 dark:bg-neutral-700/10"
>
  <img src={svgInfo.route} alt={svgInfo.title} class="mb-4 mt-2 h-10" />
  <div class="mb-3 flex flex-col items-center justify-center">
    <p class="truncate text-[15px] font-medium">{svgInfo.title}</p>
    <a
      href={`/directory/${svgInfo.category.toLowerCase()}`}
      class="text-sm lowercase text-neutral-500 hover:underline">{svgInfo.category}</a
    >
  </div>
  <div class="flex items-center space-x-1">
    <button
      title="Copy to clipboard"
      on:click={() => {
        copyToClipboard(svgInfo.route);
      }}
      class="flex items-center space-x-2 rounded-md p-2 duration-100 hover:bg-neutral-200 dark:hover:bg-neutral-700/40"
    >
      <Copy size={17} />
    </button>
    <button
      title="Download"
      on:click={() => {
        downloadSvg(svgInfo.route);
      }}
      class="flex items-center space-x-2 rounded-md p-2 duration-100 hover:bg-neutral-200 dark:hover:bg-neutral-700/40"
    >
      <DownloadSimple size={17} />
    </button>
    <a
      href={svgInfo.url}
      title="Website"
      target="_blank"
      class="flex items-center space-x-2 rounded-md p-2 duration-100 hover:bg-neutral-200 dark:hover:bg-neutral-700/40"
    >
      <ArrowUpRight size={17} />
    </a>
  </div>
</div>
