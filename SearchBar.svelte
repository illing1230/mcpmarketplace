<script>
    export let searchQuery = '';
    export let sortBy = 'popularity';
    export let onSearchChange;
    export let onSortChange;
</script>

<div class="bg-white p-6 rounded-xl shadow-sm border border-gray-200 mb-8">
    <div class="flex flex-col sm:flex-row gap-4">
        <div class="flex-1 relative">
            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                <svg class="h-5 w-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                </svg>
            </div>
            <input
                type="text"
                bind:value={searchQuery}
                on:input={onSearchChange}
                placeholder="MCP, 도구, 카테고리 검색..."
                class="block w-full pl-10 pr-3 py-3 border border-gray-300 rounded-lg leading-5 bg-white placeholder-gray-500 focus:outline-none focus:placeholder-gray-400 focus:ring-2 focus:ring-blue-500 focus:border-blue-500 text-sm transition-all"
            />
        </div>
        
        <div class="sm:w-48">
            <select
                bind:value={sortBy}
                on:change={onSortChange}
                class="block w-full px-3 py-3 border border-gray-300 rounded-lg leading-5 bg-white focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 text-sm transition-all"
            >
                <option value="popularity">인기순</option>
                <option value="rating">평점순</option>
                <option value="newest">최신순</option>
                <option value="name">이름순</option>
            </select>
        </div>
    </div>
</div>