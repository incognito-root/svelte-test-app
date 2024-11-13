<script>
  import { cn } from './../../utils/utils'

  export let style = 'padding-left: 40px;'
  export let placeholder = 'Search...'
  export let searchQuery
</script>

<div class="w-full mb-5 relative">
  <div class="absolute left-3 top-1/2 transform -translate-y-1/2">
    <img 
      src="src/assets/icons/search-01.png" 
      alt="Search"
      class="w-4 h-4"
    />
  </div>
  <input
    class={cn(
      `w-full py-2 text-black bg-[#F7F7F8] border-slate-200 rounded-md
      outline-none focus:outline-none focus:ring-0
      placeholder:text-gray-500 placeholder:text-sm`,
      style,
    )}
    type="text"
    bind:value={$searchQuery}
    {placeholder}
    style="padding-left: 40px !important;"
  />
</div>