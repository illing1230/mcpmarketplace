<script>
    export let categories = [];
    export let tags = [];
    export let selectedCategory = 'all';
    export let selectedTags = [];
    export let hasActiveFilters = false;
    export let onCategoryChange;
    export let onTagChange;
    export let onClearFilters;

    function formatCategoryName(category) {
        const categoryNames = {
            'file-system': '파일 시스템',
            'database': '데이터베이스',
            'web-scraping': '웹 스크래핑',
            'api-integration': 'API 통합',
            'communication': '커뮤니케이션',
            'productivity': '생산성',
            'ai-vision': 'AI 비전',
            'document-processing': '문서 처리',
            'cryptocurrency': '암호화폐',
            'social-media': '소셜 미디어',
            'cloud-storage': '클라우드 스토리지',
            'automation': '자동화',
            'development': '개발 도구',
            'security': '보안'
        };
        return categoryNames[category] || category;
    }
</script>

<aside class="lg:w-64 flex-shrink-0">
    <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-200 h-fit">
        <div class="flex items-center justify-between mb-4">
            <h3 class="text-lg font-medium text-gray-900">필터</h3>
            {#if hasActiveFilters}
                <button
                    on:click={onClearFilters}
                    class="text-sm text-blue-600 hover:text-blue-500 font-medium"
                >
                    전체 초기화
                </button>
            {/if}
        </div>
        
        <!-- Categories -->
        <div class="mb-6">
            <h4 class="text-sm font-medium text-gray-900 mb-3">카테고리</h4>
            <div class="space-y-2">
                <label class="flex items-center cursor-pointer hover:bg-gray-50 p-2 rounded-md transition-colors">
                    <input
                        type="radio"
                        name="category"
                        value="all"
                        checked={selectedCategory === 'all'}
                        on:change={() => onCategoryChange('all')}
                        class="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300"
                    />
                    <span class="ml-2 text-sm text-gray-700">모든 카테고리</span>
                </label>
                {#each categories as category}
                    <label class="flex items-center cursor-pointer hover:bg-gray-50 p-2 rounded-md transition-colors">
                        <input
                            type="radio"
                            name="category"
                            value={category}
                            checked={selectedCategory === category}
                            on:change={() => onCategoryChange(category)}
                            class="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300"
                        />
                        <span class="ml-2 text-sm text-gray-700">{formatCategoryName(category)}</span>
                    </label>
                {/each}
            </div>
        </div>
        
        <!-- Tags -->
        <div>
            <h4 class="text-sm font-medium text-gray-900 mb-3">태그</h4>
            <div class="space-y-2 max-h-64 overflow-y-auto">
                {#each tags as tag}
                    <label class="flex items-center cursor-pointer hover:bg-gray-50 p-2 rounded-md transition-colors">
                        <input
                            type="checkbox"
                            checked={selectedTags.includes(tag)}
                            on:change={(e) => onTagChange(tag, e.target.checked)}
                            class="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded"
                        />
                        <span class="ml-2 text-sm text-gray-700">{tag}</span>
                    </label>
                {/each}
            </div>
        </div>
    </div>
</aside>