<script>
    import { onMount } from 'svelte';
    import Header from '$lib/components/Header.svelte';
    import SearchBar from '$lib/components/SearchBar.svelte';
    import FilterSidebar from '$lib/components/FilterSidebar.svelte';
    import LoadingSpinner from '$lib/components/LoadingSpinner.svelte';
    import NoResults from '$lib/components/NoResults.svelte';
    import MCPGrid from '$lib/components/MCPGrid.svelte';
    import MCPModal from '$lib/components/MCPModal.svelte';

    let mcps = [];
    let filteredMcps = [];
    let categories = [];
    let tags = [];
    let selectedCategory = 'all';
    let selectedTags = [];
    let searchQuery = '';
    let sortBy = 'popularity';
    let isLoading = true;
    let selectedMcp = null;
    let isModalVisible = false;

    onMount(async () => {
        await loadData();
        filterAndRender();
        isLoading = false;
    });

    async function loadData() {
        try {
            const [mcpsResponse, categoriesResponse, tagsResponse] = await Promise.all([
                fetch('/api/mcps'),
                fetch('/api/categories'),
                fetch('/api/tags')
            ]);
            
            if (!mcpsResponse.ok || !categoriesResponse.ok || !tagsResponse.ok) {
                throw new Error('네트워크 응답이 정상이 아닙니다');
            }
            
            const mcpsData = await mcpsResponse.json();
            const categoriesData = await categoriesResponse.json();
            const tagsData = await tagsResponse.json();
            
            mcps = mcpsData.mcps || [];
            categories = categoriesData.categories || [];
            tags = tagsData.tags || [];
            
        } catch (error) {
            console.error('데이터 로딩 실패:', error);
        }
    }

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

    function filterAndRender() {
        let filtered = [...mcps];
        
        // Search filter
        if (searchQuery.trim()) {
            const query = searchQuery.toLowerCase();
            filtered = filtered.filter(mcp => 
                mcp.name.toLowerCase().includes(query) ||
                mcp.description.toLowerCase().includes(query) ||
                mcp.tags.some(tag => tag.toLowerCase().includes(query))
            );
        }
        
        // Category filter
        if (selectedCategory !== 'all') {
            filtered = filtered.filter(mcp => mcp.category === selectedCategory);
        }
        
        // Tags filter
        if (selectedTags.length > 0) {
            filtered = filtered.filter(mcp => 
                selectedTags.some(tag => mcp.tags.includes(tag))
            );
        }
        
        // Sort
        switch (sortBy) {
            case 'popularity':
                filtered.sort((a, b) => b.downloads - a.downloads);
                break;
            case 'rating':
                filtered.sort((a, b) => b.rating - a.rating);
                break;
            case 'newest':
                filtered.sort((a, b) => new Date(b.lastUpdated) - new Date(a.lastUpdated));
                break;
            case 'name':
                filtered.sort((a, b) => a.name.localeCompare(b.name));
                break;
        }
        
        filteredMcps = filtered;
    }

    function handleSearchChange() {
        filterAndRender();
    }

    function handleSortChange() {
        filterAndRender();
    }

    function handleCategoryChange(category) {
        selectedCategory = category;
        filterAndRender();
    }

    function handleTagChange(tag, checked) {
        if (checked) {
            selectedTags = [...selectedTags, tag];
        } else {
            selectedTags = selectedTags.filter(t => t !== tag);
        }
        filterAndRender();
    }

    function clearAllFilters() {
        selectedCategory = 'all';
        selectedTags = [];
        searchQuery = '';
        filterAndRender();
    }

    function openModal(mcp) {
        selectedMcp = mcp;
        isModalVisible = true;
    }

    function closeModal() {
        isModalVisible = false;
        selectedMcp = null;
    }

    async function handleMcpSubmit(mcpData) {
        try {
            // 새로운 ID 생성 (기존 MCP 중 가장 큰 ID + 1)
            const maxId = Math.max(...mcps.map(mcp => mcp.id), 0);
            const newMcp = {
                ...mcpData,
                id: maxId + 1
            };

            // 로컬 상태 업데이트
            mcps = [...mcps, newMcp];
            
            // 서버에 데이터 저장 (JSON Server API 사용)
            const response = await fetch('/api/mcps', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(newMcp)
            });

            if (!response.ok) {
                throw new Error('MCP 제출에 실패했습니다.');
            }

            // 필터링 다시 적용
            filterAndRender();
            
            // 성공 메시지 (선택적으로 알림 컴포넌트 추가 가능)
            console.log('MCP가 성공적으로 제출되었습니다!');
        } catch (error) {
            console.error('MCP 제출 오류:', error);
            // 에러 처리 (선택적으로 에러 알림 컴포넌트 추가 가능)
        }
    }

    $: totalDownloads = mcps.reduce((sum, mcp) => sum + mcp.downloads, 0);
    $: hasActiveFilters = selectedCategory !== 'all' || selectedTags.length > 0 || searchQuery.trim();
</script>

<svelte:head>
    <title>MCP Marketplace - Model Context Protocol 서버 발견하기</title>
    <meta name="description" content="Korean language marketplace for Model Context Protocol servers with filtering, search, and detailed MCP information" />
</svelte:head>

<Header 
    totalMcps={mcps.length} 
    totalCategories={categories.length} 
    totalDownloads={totalDownloads} 
    onMcpSubmit={handleMcpSubmit}
/>

<main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
    <SearchBar 
        bind:searchQuery={searchQuery}
        bind:sortBy={sortBy}
        onSearchChange={handleSearchChange}
        onSortChange={handleSortChange}
    />

    <div class="flex flex-col lg:flex-row gap-8">
        <FilterSidebar 
            {categories}
            {tags}
            {selectedCategory}
            {selectedTags}
            {hasActiveFilters}
            onCategoryChange={handleCategoryChange}
            onTagChange={handleTagChange}
            onClearFilters={clearAllFilters}
        />
        
        <!-- Main Content -->
        <main class="flex-1 min-w-0">
            {#if isLoading}
                <LoadingSpinner />
            {:else}
                <!-- Results Header -->
                <div class="flex items-center justify-between mb-6">
                    <h2 class="text-xl font-semibold text-gray-900">
                        {filteredMcps.length}개 MCP 발견
                    </h2>
                    <div class="hidden sm:block text-sm text-gray-600">
                        Model Context Protocol 서버와 도구 검색 결과
                    </div>
                </div>
                
                {#if filteredMcps.length === 0}
                    <NoResults />
                {:else}
                    <MCPGrid mcps={filteredMcps} onCardClick={openModal} />
                {/if}
            {/if}
        </main>
    </div>
</main>

<!-- Modal -->
<MCPModal 
    mcp={selectedMcp} 
    isVisible={isModalVisible} 
    onClose={closeModal} 
/>