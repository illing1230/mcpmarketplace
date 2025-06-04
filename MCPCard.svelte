<script>
    export let mcp;
    export let onCardClick;

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

    function createStars(rating) {
        const stars = [];
        const fullStars = Math.floor(rating);
        const hasHalfStar = rating % 1 >= 0.5;
        
        for (let i = 0; i < fullStars; i++) {
            stars.push('★');
        }
        
        if (hasHalfStar) {
            stars.push('½');
        }
        
        const remainingStars = 5 - fullStars - (hasHalfStar ? 1 : 0);
        for (let i = 0; i < remainingStars; i++) {
            stars.push('☆');
        }
        
        return stars.join('');
    }

    function formatDownloads(downloads) {
        if (downloads >= 1000000) {
            return (downloads / 1000000).toFixed(1) + 'M';
        } else if (downloads >= 1000) {
            return (downloads / 1000).toFixed(1) + 'K';
        }
        return downloads.toString();
    }

    function getTimeAgo(dateString) {
        const date = new Date(dateString);
        const now = new Date();
        const diffTime = Math.abs(now - date);
        const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
        
        if (diffDays === 1) {
            return '1일 전';
        } else if (diffDays < 30) {
            return `${diffDays}일 전`;
        } else if (diffDays < 365) {
            const months = Math.floor(diffDays / 30);
            return `${months}개월 전`;
        } else {
            const years = Math.floor(diffDays / 365);
            return `${years}년 전`;
        }
    }
</script>

<div 
    class="bg-white rounded-xl shadow-sm border border-gray-200 card-hover cursor-pointer group"
    on:click={() => onCardClick(mcp)}
    role="button"
    tabindex="0"
    on:keydown={(e) => e.key === 'Enter' && onCardClick(mcp)}
>
    <div class="p-6">
        <!-- Header -->
        <div class="flex items-start justify-between mb-3">
            <div class="flex-1 min-w-0">
                <h3 class="text-lg font-semibold text-gray-900 group-hover:text-blue-600 transition-colors truncate">
                    {mcp.name}
                </h3>
                <p class="text-sm text-gray-600 mt-1">by {mcp.author}</p>
            </div>
            <div class="flex items-center space-x-2 ml-4">
                <span class="inline-flex items-center px-2 py-1 rounded-md text-xs font-medium bg-gray-100 text-gray-800 capitalize">
                    {formatCategoryName(mcp.category)}
                </span>
            </div>
        </div>
        
        <!-- Description -->
        <p class="text-gray-700 text-sm mb-4 line-clamp-2">
            {mcp.description}
        </p>
        
        <!-- Tags -->
        <div class="flex flex-wrap gap-1 mb-4">
            {#each mcp.tags.slice(0, 3) as tag}
                <span class="inline-flex items-center px-2 py-1 rounded-md text-xs font-medium bg-blue-50 text-blue-700">
                    {tag}
                </span>
            {/each}
            {#if mcp.tags.length > 3}
                <span class="inline-flex items-center px-2 py-1 rounded-md text-xs font-medium bg-gray-50 text-gray-500">
                    +{mcp.tags.length - 3} more
                </span>
            {/if}
        </div>
        
        <!-- Stats -->
        <div class="flex items-center justify-between">
            <div class="flex items-center space-x-4 text-sm text-gray-600">
                <!-- Rating -->
                <div class="flex items-center space-x-1">
                    <div class="flex items-center text-yellow-400">
                        {createStars(mcp.rating)}
                    </div>
                    <span class="font-medium">{mcp.rating}</span>
                </div>
                
                <!-- Downloads -->
                <div class="flex items-center space-x-1">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M9 19l3 3m0 0l3-3m-3 3V10"></path>
                    </svg>
                    <span>{formatDownloads(mcp.downloads)}</span>
                </div>
            </div>
            
            <!-- Last Updated -->
            <div class="text-xs text-gray-500">
                {getTimeAgo(mcp.lastUpdated)}
            </div>
        </div>
    </div>
</div>