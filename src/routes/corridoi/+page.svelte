<script>
    import { datiCondivisi } from '$lib/stores.js';
    import Card from '$lib/Card.svelte';
    import { onMount } from 'svelte';
    
    let articoli = [];
    let loading = false;
    let error = null;
    let highlightedProductId = null;
    let pagina = "corridoi"
    
    const categoria = $datiCondivisi.cat;
    const searchedProduct = $datiCondivisi.searchedProduct;
    
    async function fetchArticoli(categoria) {
        loading = true;
        error = null;
        articoli = [];
        
        try {
            const res = await fetch(`https://backendls-1.onrender.com/articoli/categoria/${categoria}`, {
                method: "GET",
                headers: {
                    "Content-Type": "application/json"
                },
                credentials: 'include'
            });
           
            if (res.ok) {
                const data = await res.json();
                console.log(data);
                
                if (Array.isArray(data)) {
                    data.forEach(articolo => {
                        articoli = [...articoli, articolo];
                    });
                    console.log(`Caricati ${articoli.length} articoli per la categoria: ${categoria}`);
                    
                    if (searchedProduct) {
                        highlightedProductId = searchedProduct._id || searchedProduct.Id_art;
                        setTimeout(() => {
                            scrollToHighlightedProduct();
                        }, 100);
                    }
                } else {
                    error = "Formato dati non valido - expected array";
                    console.error("Data received is not an array:", data);
                }
            } else {
                error = `Errore HTTP: ${res.status}`;
                console.log("Error:", error);
            }
        } catch (networkError) {
            error = `Errore di rete: ${networkError.message}`;
            console.log("Errore di rete:", networkError);
        } finally {
            loading = false;
        }
    }

    async function fetchAllArticoli() {
        loading = true;
        error = null;
        articoli = [];
        
        try {
            const res = await fetch(`http://localhost:3080/articoli/`, {
                method: "GET",
                headers: {
                    "Content-Type": "application/json"
                },
                credentials: 'include'
            });
           
            if (res.ok) {
                const data = await res.json();
                console.log(data);
                
                if (Array.isArray(data)) {
                    data.forEach(articolo => {
                        articoli = [...articoli, articolo];
                    });
                    console.log(`Caricati ${articoli.length} articoli per la categoria: ${categoria}`);
                    
                    if (searchedProduct) {
                        highlightedProductId = searchedProduct._id || searchedProduct.Id_art;
                        setTimeout(() => {
                            scrollToHighlightedProduct();
                        }, 100);
                    }
                } else {
                    error = "Formato dati non valido - expected array";
                    console.error("Data received is not an array:", data);
                }
            } else {
                error = `Errore HTTP: ${res.status}`;
                console.log("Error:", error);
            }
        } catch (networkError) {
            error = `Errore di rete: ${networkError.message}`;
            console.log("Errore di rete:", networkError);
        } finally {
            loading = false;
        }
    }
    
    function scrollToHighlightedProduct() {
        if (highlightedProductId) {
            const element = document.getElementById(`product-${highlightedProductId}`);
            if (element) {
                element.scrollIntoView({ 
                    behavior: 'smooth', 
                    block: 'center'
                });
                element.classList.add('pulse-effect');
                setTimeout(() => {
                    element.classList.remove('pulse-effect');
                }, 3000);
            }
        }
    }
    
    function isHighlighted(articolo) {
        if (!highlightedProductId) return false;
        return articolo._id === highlightedProductId || articolo.Id_art === highlightedProductId;
    }
    
    onMount(() => {
        if (categoria) {
            if (categoria == "Tutti gli articoli"){
                fetchAllArticoli();
            }else{
            fetchArticoli(categoria);
            }
        }
    });
</script>

<div class="header-container">
    <h1>{categoria}</h1>
    {#if searchedProduct}
        <div class="search-info">
            <span class="search-badge">🔍 Prodotto cercato: "{searchedProduct.nome_prodotto}"</span>
        </div>
    {/if}
</div>

{#if loading}
    <div class="loading">
        <p>Caricamento prodotti...</p>
    </div>
{/if}

{#if error}
    <div class="error">
        <p>❌ {error}</p>
    </div>
{/if}

{#if articoli.length > 0}
    <div class="products-grid">
        {#each articoli as articolo, index (index)}
            <div 
                id="product-{articolo._id || articolo.Id_art}"
                class="product-container"
                class:highlighted={isHighlighted(articolo)}
            >
                <Card {articolo} {pagina}/>
                {#if isHighlighted(articolo)}
                    <div class="highlight-label">
                        ⭐ Prodotto cercato
                    </div>
                {/if}
            </div>
        {/each}
    </div>
{:else if !loading && !error}
    <div class="no-products">
        <p>Nessun prodotto trovato per la categoria: {categoria}</p>
    </div>
{/if}

<style>
    .header-container {
        margin-bottom: 24px;
    }
    
    .search-info {
        margin-top: 12px;
        text-align: center;
    }
    
    .search-badge {
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        color: white;
        padding: 8px 16px;
        border-radius: 20px;
        font-size: 14px;
        font-weight: 600;
        display: inline-block;
        box-shadow: 0 2px 8px rgba(102, 126, 234, 0.3);
    }
    
    .loading {
        text-align: center;
        padding: 40px;
        color: #4a5568;
    }
    
    .error {
        background: #fed7d7;
        color: #c53030;
        padding: 16px;
        border-radius: 8px;
        margin-bottom: 24px;
        text-align: center;
    }
    
    .products-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
        gap: 24px;
        margin: 24px 0;
    }
    
    .product-container {
        position: relative;
        transition: all 0.3s ease;
    }
    
    .product-container.highlighted {
        transform: scale(1.02);
        z-index: 10;
    }
    
    .product-container.highlighted :global(.product-card) {
        border: 3px solid #667eea;
        box-shadow: 0 8px 32px rgba(102, 126, 234, 0.3);
        background: linear-gradient(135deg, #f8f9ff 0%, #ffffff 100%);
    }
    
    .highlight-label {
        position: absolute;
        top: -10px;
        left: 50%;
        transform: translateX(-50%);
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        color: white;
        padding: 6px 12px;
        border-radius: 12px;
        font-size: 12px;
        font-weight: 700;
        box-shadow: 0 2px 8px rgba(102, 126, 234, 0.4);
        z-index: 11;
        animation: bounce 0.6s ease-in-out;
    }
    
    @keyframes bounce {
        0%, 20%, 50%, 80%, 100% {
            transform: translateX(-50%) translateY(0);
        }
        40% {
            transform: translateX(-50%) translateY(-10px);
        }
        60% {
            transform: translateX(-50%) translateY(-5px);
        }
    }
    
    :global(.pulse-effect) {
        animation: pulse 1s ease-in-out 3;
    }
    
    @keyframes pulse {
        0% {
            transform: scale(1);
        }
        50% {
            transform: scale(1.05);
        }
        100% {
            transform: scale(1);
        }
    }
    
    .no-products {
        text-align: center;
        padding: 40px;
        color: #718096;
        background: #f7fafc;
        border-radius: 8px;
    }
    
    h1 {
        color: #2d3748;
        margin-bottom: 24px;
        text-transform: capitalize;
        text-align: center;
    }
</style>