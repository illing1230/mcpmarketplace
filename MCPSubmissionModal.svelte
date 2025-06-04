<script>
    export let isVisible = false;
    export let onClose;
    export let onSubmit;

    let formData = {
        name: '',
        description: '',
        author: '',
        version: '1.0.0',
        category: '',
        tags: '',
        repository: '',
        documentation: '',
        license: 'MIT',
        installCommand: '',
        features: '',
        requirements: ''
    };

    let isSubmitting = false;
    let errors = {};

    function handleKeydown(event) {
        if (event.key === 'Escape') {
            closeModal();
        }
    }

    function handleBackdropClick(event) {
        if (event.target === event.currentTarget) {
            closeModal();
        }
    }

    function closeModal() {
        onClose();
        resetForm();
    }

    function resetForm() {
        formData = {
            name: '',
            description: '',
            author: '',
            version: '1.0.0',
            category: '',
            tags: '',
            repository: '',
            documentation: '',
            license: 'MIT',
            installCommand: '',
            features: '',
            requirements: ''
        };
        errors = {};
        isSubmitting = false;
    }

    function validateForm() {
        errors = {};
        
        if (!formData.name.trim()) {
            errors.name = 'MCP 이름을 입력해주세요.';
        }
        
        if (!formData.description.trim()) {
            errors.description = '설명을 입력해주세요.';
        }
        
        if (!formData.author.trim()) {
            errors.author = '작성자를 입력해주세요.';
        }
        
        if (!formData.category.trim()) {
            errors.category = '카테고리를 선택해주세요.';
        }
        
        if (!formData.repository.trim()) {
            errors.repository = '저장소 URL을 입력해주세요.';
        } else if (!isValidUrl(formData.repository)) {
            errors.repository = '올바른 URL을 입력해주세요.';
        }
        
        return Object.keys(errors).length === 0;
    }

    function isValidUrl(string) {
        try {
            new URL(string);
            return true;
        } catch (_) {
            return false;
        }
    }

    async function handleSubmit() {
        if (!validateForm()) {
            return;
        }

        isSubmitting = true;

        try {
            // 태그를 배열로 변환
            const tagsArray = formData.tags.split(',').map(tag => tag.trim()).filter(tag => tag);
            
            // 기능을 배열로 변환
            const featuresArray = formData.features.split('\n').map(feature => feature.trim()).filter(feature => feature);

            const mcpData = {
                ...formData,
                tags: tagsArray,
                features: featuresArray,
                downloads: 0,
                rating: 0,
                reviews: 0,
                lastUpdated: new Date().toISOString(),
                verified: false,
                trending: false
            };

            await onSubmit(mcpData);
            closeModal();
        } catch (error) {
            console.error('MCP 제출 오류:', error);
        } finally {
            isSubmitting = false;
        }
    }

    const categories = [
        'development',
        'data-analysis', 
        'automation',
        'ai-ml',
        'web-scraping',
        'file-management',
        'communication',
        'productivity',
        'security',
        'monitoring'
    ];

    function formatCategoryName(category) {
        const categoryNames = {
            'development': '개발',
            'data-analysis': '데이터 분석',
            'automation': '자동화',
            'ai-ml': 'AI/ML',
            'web-scraping': '웹 스크래핑',
            'file-management': '파일 관리',
            'communication': '커뮤니케이션',
            'productivity': '생산성',
            'security': '보안',
            'monitoring': '모니터링'
        };
        return categoryNames[category] || category;
    }
</script>

<svelte:window on:keydown={handleKeydown} />

{#if isVisible}
    <div 
        class="fixed inset-0 bg-black bg-opacity-50 modal-backdrop flex items-center justify-center p-4 z-50"
        on:click={handleBackdropClick}
        on:keydown={handleKeydown}
        role="dialog"
        tabindex="-1"
        aria-modal="true"
        aria-labelledby="submission-modal-title"
    >
        <div class="bg-white rounded-xl shadow-xl max-w-4xl w-full max-h-[90vh] overflow-y-auto">
            <div class="p-8">
                <!-- Header -->
                <div class="flex items-start justify-between mb-6">
                    <div>
                        <h2 id="submission-modal-title" class="text-2xl font-bold text-gray-900 mb-2">
                            새 MCP 제출하기
                        </h2>
                        <p class="text-gray-600">
                            Model Context Protocol 서버를 커뮤니티와 공유해보세요.
                        </p>
                    </div>
                    <button
                        on:click={closeModal}
                        class="text-gray-400 hover:text-gray-600 transition-colors p-2"
                        aria-label="모달 닫기"
                    >
                        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                        </svg>
                    </button>
                </div>

                <!-- Form -->
                <form on:submit|preventDefault={handleSubmit} class="space-y-6">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <!-- MCP 이름 -->
                        <div>
                            <label for="name" class="block text-sm font-medium text-gray-700 mb-2">
                                MCP 이름 *
                            </label>
                            <input
                                id="name"
                                type="text"
                                bind:value={formData.name}
                                placeholder="예: weather-mcp"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                class:border-red-500={errors.name}
                                required
                            />
                            {#if errors.name}
                                <p class="text-red-600 text-sm mt-1">{errors.name}</p>
                            {/if}
                        </div>

                        <!-- 작성자 -->
                        <div>
                            <label for="author" class="block text-sm font-medium text-gray-700 mb-2">
                                작성자 *
                            </label>
                            <input
                                id="author"
                                type="text"
                                bind:value={formData.author}
                                placeholder="예: OpenAI"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                class:border-red-500={errors.author}
                                required
                            />
                            {#if errors.author}
                                <p class="text-red-600 text-sm mt-1">{errors.author}</p>
                            {/if}
                        </div>

                        <!-- 버전 -->
                        <div>
                            <label for="version" class="block text-sm font-medium text-gray-700 mb-2">
                                버전
                            </label>
                            <input
                                id="version"
                                type="text"
                                bind:value={formData.version}
                                placeholder="예: 1.0.0"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                            />
                        </div>

                        <!-- 카테고리 -->
                        <div>
                            <label for="category" class="block text-sm font-medium text-gray-700 mb-2">
                                카테고리 *
                            </label>
                            <select
                                id="category"
                                bind:value={formData.category}
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                class:border-red-500={errors.category}
                                required
                            >
                                <option value="">카테고리 선택</option>
                                {#each categories as category}
                                    <option value={category}>{formatCategoryName(category)}</option>
                                {/each}
                            </select>
                            {#if errors.category}
                                <p class="text-red-600 text-sm mt-1">{errors.category}</p>
                            {/if}
                        </div>
                    </div>

                    <!-- 설명 -->
                    <div>
                        <label for="description" class="block text-sm font-medium text-gray-700 mb-2">
                            설명 *
                        </label>
                        <textarea
                            id="description"
                            bind:value={formData.description}
                            placeholder="MCP 서버의 기능과 용도를 설명해주세요..."
                            rows="4"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                            class:border-red-500={errors.description}
                            required
                        ></textarea>
                        {#if errors.description}
                            <p class="text-red-600 text-sm mt-1">{errors.description}</p>
                        {/if}
                    </div>

                    <!-- 태그 -->
                    <div>
                        <label for="tags" class="block text-sm font-medium text-gray-700 mb-2">
                            태그
                        </label>
                        <input
                            id="tags"
                            type="text"
                            bind:value={formData.tags}
                            placeholder="예: weather, api, real-time (쉼표로 구분)"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                        />
                        <p class="text-gray-500 text-sm mt-1">쉼표로 구분하여 입력해주세요.</p>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <!-- 저장소 URL -->
                        <div>
                            <label for="repository" class="block text-sm font-medium text-gray-700 mb-2">
                                저장소 URL *
                            </label>
                            <input
                                id="repository"
                                type="url"
                                bind:value={formData.repository}
                                placeholder="https://github.com/username/mcp-name"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                                class:border-red-500={errors.repository}
                                required
                            />
                            {#if errors.repository}
                                <p class="text-red-600 text-sm mt-1">{errors.repository}</p>
                            {/if}
                        </div>

                        <!-- 문서 URL -->
                        <div>
                            <label for="documentation" class="block text-sm font-medium text-gray-700 mb-2">
                                문서 URL
                            </label>
                            <input
                                id="documentation"
                                type="url"
                                bind:value={formData.documentation}
                                placeholder="https://docs.example.com"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                            />
                        </div>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <!-- 라이센스 -->
                        <div>
                            <label for="license" class="block text-sm font-medium text-gray-700 mb-2">
                                라이센스
                            </label>
                            <select
                                id="license"
                                bind:value={formData.license}
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                            >
                                <option value="MIT">MIT</option>
                                <option value="Apache-2.0">Apache 2.0</option>
                                <option value="GPL-3.0">GPL 3.0</option>
                                <option value="BSD-3-Clause">BSD 3-Clause</option>
                                <option value="ISC">ISC</option>
                                <option value="Other">기타</option>
                            </select>
                        </div>

                        <!-- 설치 명령어 -->
                        <div>
                            <label for="installCommand" class="block text-sm font-medium text-gray-700 mb-2">
                                설치 명령어
                            </label>
                            <input
                                id="installCommand"
                                type="text"
                                bind:value={formData.installCommand}
                                placeholder="예: pip install mcp-weather"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                            />
                        </div>
                    </div>

                    <!-- 주요 기능 -->
                    <div>
                        <label for="features" class="block text-sm font-medium text-gray-700 mb-2">
                            주요 기능
                        </label>
                        <textarea
                            id="features"
                            bind:value={formData.features}
                            placeholder="각 기능을 새 줄에 입력해주세요&#10;예:&#10;실시간 날씨 정보 조회&#10;5일 예보 제공&#10;지역별 날씨 알림"
                            rows="4"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                        ></textarea>
                        <p class="text-gray-500 text-sm mt-1">각 기능을 새 줄에 입력해주세요.</p>
                    </div>

                    <!-- 요구사항 -->
                    <div>
                        <label for="requirements" class="block text-sm font-medium text-gray-700 mb-2">
                            요구사항
                        </label>
                        <textarea
                            id="requirements"
                            bind:value={formData.requirements}
                            placeholder="설치 및 사용을 위한 요구사항을 입력해주세요..."
                            rows="3"
                            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                        ></textarea>
                    </div>

                    <!-- 제출 버튼 -->
                    <div class="flex justify-end space-x-4 pt-6 border-t border-gray-200">
                        <button
                            type="button"
                            on:click={closeModal}
                            class="px-6 py-3 border border-gray-300 rounded-md text-gray-700 bg-white hover:bg-gray-50 transition-colors"
                            disabled={isSubmitting}
                        >
                            취소
                        </button>
                        <button
                            type="submit"
                            class="px-6 py-3 bg-gradient-to-r from-blue-500 to-purple-600 text-white rounded-md hover:from-blue-600 hover:to-purple-700 transition-all duration-200 shadow-md hover:shadow-lg disabled:opacity-50 disabled:cursor-not-allowed"
                            disabled={isSubmitting}
                        >
                            {#if isSubmitting}
                                <div class="flex items-center">
                                    <div class="w-4 h-4 border-2 border-white border-t-transparent rounded-full animate-spin mr-2"></div>
                                    제출 중...
                                </div>
                            {:else}
                                MCP 제출하기
                            {/if}
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
{/if}

<style>
    .modal-backdrop {
        backdrop-filter: blur(2px);
    }
</style>