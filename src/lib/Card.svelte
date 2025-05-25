<script>
    import { goto } from "$app/navigation";
    import { invalidateAll } from '$app/navigation';

  export let articolo = {};
  export let pagina = {};
  
  const formatPrice = (price) => {
    return price?.replace('$', '€') || 'N/A';
  };
  
  const getStatusColor = (stato) => {
    switch(stato?.toLowerCase()) {
      case 'disponibile': return 'text-green-600 bg-green-100';
      case 'esaurito': return 'text-red-600 bg-red-100';
      case 'in arrivo': return 'text-yellow-600 bg-yellow-100';
      default: return 'text-gray-600 bg-gray-100';
    }
  };
  
  const generateStars = (rating) => {
    if (!rating) return '';
    const numRating = parseFloat(rating.split('/')[0]);
    const fullStars = Math.floor(numRating);
    const hasHalfStar = numRating % 1 !== 0;
    
    return '★'.repeat(fullStars) + (hasHalfStar ? '☆' : '') + '☆'.repeat(5 - Math.ceil(numRating));
  };

  const getButtonClasses = (pagina) => {
    const baseClasses = "add-to-cart-btn";
    
    switch(pagina?.toLowerCase()) {
      case 'carrello':
        return `${baseClasses} btn-remove`;
      case 'corridoi':
        return `${baseClasses} btn-add`;
      default:
        return `${baseClasses} btn-default`;
    }
  };

  const getButtonText = (pagina, statoArticolo) => {
    if (statoArticolo?.toLowerCase() === 'esaurito') {
      return '🚫 Non Disponibile';
    }
    
    switch(pagina?.toLowerCase()) {
      case 'carrello':
        return '🗑️ Rimuovi dal Carrello';
      case 'corridoi':
        return '🛒 Aggiungi al Carrello';
      default:
        return '📦 Azione Prodotto';
    }
  };

  async function aggiungiAlCarrello() {
    try {
      const res = await fetch(`https://backendls-1.onrender.com/carrello/utente/aggiungi`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        credentials: 'include',
        body: JSON.stringify(articolo)
      });

      if (res.ok) {
        alert('Prodotto aggiunto al carrello!');
      } else {
        const data = await res.json();
        alert(data.message || 'Errore nell\'aggiungere il prodotto al carrello');
      }
    } catch (err) {
      console.error('Errore:', err);
      alert('Errore di rete nell\'aggiungere il prodotto al carrello');
    }
  }

  async function rimuoviArticolo() {
    try {
      const res = await fetch(`http://localhost:3080/rimuoviArticolo/utente/${articolo.Id_art}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        credentials: 'include',
      });

      if (res.ok) {
        window.location.reload()
      } else {
        const data = await res.json();
        alert(data.message || 'Errore nel rimuovere il prodotto dal carrello');
      }
    } catch (err) {
      console.error('Errore:', err);
      alert('Errore di rete nel rimuovere il prodotto dal carrello');
    }
  } 
  
  function azione() {
    console.log('Pagina:', pagina, 'Azione richiesta per articolo:', articolo.Id_art);
    if (pagina?.toLowerCase() === "carrello") {    
      rimuoviArticolo();
    } else if (pagina?.toLowerCase() === "corridoi") {
      aggiungiAlCarrello();
    } else {
      console.log('Pagina non riconosciuta:', pagina);
    }
  }
</script>

<div class="product-card">
  <div class="card-header">
    <h3 class="product-name">{articolo.nome_prodotto || 'N/A'}</h3>
    <span class="brand">{articolo.marca}</span>
  </div>
  
  <p class="description">{articolo.descrizione_prodotto || 'N/A'}</p>
  
  <div class="price-section">
    <div class="price-container">
      <span class="price">{formatPrice(articolo.prezzo) || 'N/A'}</span>
      <span class="quantity">/ {articolo.quantità || 'unità'}</span>
    </div>
    {#if articolo.sconto && articolo.sconto !== '0%'}
      <span class="discount">-{articolo.sconto}</span>
    {/if}
  </div>
  
  {#if articolo.rating}
    <div class="rating">
      <span class="stars">{generateStars(articolo.rating)}</span>
      <span class="rating-text">{articolo.rating}</span>
    </div>
  {/if}
  
  <div class="status-container">
    <span class="status {getStatusColor(articolo.stato)}">{articolo.stato || 'N/A'}</span>
  </div>
  
  <div class="details">
    <div class="detail-row">
      <span class="label">Categoria:</span>
      <span class="value">{articolo.categoria || 'N/A'}</span>
    </div>
    
    <div class="detail-row">
      <span class="label">Origine:</span>
      <span class="value">{articolo.origine || 'N/A'}</span>
    </div>
    
    <div class="detail-row">
      <span class="label">Posizione:</span>
      <span class="value">Scaffale {articolo.scaffale || 'N/A'} - Corsia {articolo.corsia || 'N/A'}</span>
    </div>
    
    {#if articolo.allergeni && articolo.allergeni !== 'nessuno'}
      <div class="detail-row">
        <span class="label">Allergeni:</span>
        <span class="value allergeni">{articolo.allergeni}</span>
      </div>
    {/if}
  </div>
  
  
  <div class="add-to-cart-container">
    <button 
      class={getButtonClasses(pagina)}
      on:click={azione}
      disabled={articolo.stato?.toLowerCase() === 'esaurito'}>
        {getButtonText(pagina, articolo.stato)}
      </button>
    </div>
  
  {#if articolo.fornitore}
    <div class="footer">
      <small class="supplier">Fornitore: {articolo.fornitore}</small>
    </div>
  {/if}
</div>

<style>
  .product-card {
    background: white;
    border: 1px solid #e2e8f0;
    border-radius: 12px;
    padding: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    max-width: 400px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    transition: box-shadow 0.2s ease;
  }
  
  .product-card:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 12px;
  }
  
  .product-name {
    font-size: 18px;
    font-weight: 600;
    color: #1a202c;
    margin: 0;
    flex: 1;
    text-transform: capitalize;
  }
  
  .brand {
    font-size: 12px;
    color: #718096;
    font-weight: 500;
    margin-left: 12px;
  }
  
  .description {
    font-size: 14px;
    color: #4a5568;
    margin-bottom: 16px;
    line-height: 1.4;
  }
  
  .price-section {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 12px;
  }
  
  .price-container {
    display: flex;
    align-items: baseline;
  }
  
  .price {
    font-size: 24px;
    font-weight: 700;
    color: #2d3748;
  }
  
  .quantity {
    font-size: 14px;
    color: #718096;
    margin-left: 4px;
  }
  
  .discount {
    background: #fed7d7;
    color: #c53030;
    padding: 4px 8px;
    border-radius: 6px;
    font-size: 12px;
    font-weight: 600;
  }
  
  .rating {
    display: flex;
    align-items: center;
    margin-bottom: 12px;
  }
  
  .stars {
    color: #ffd700;
    font-size: 16px;
    margin-right: 8px;
  }
  
  .rating-text {
    font-size: 14px;
    color: #4a5568;
    font-weight: 500;
  }
  
  .status-container {
    margin-bottom: 16px;
  }
  
  .status {
    padding: 6px 12px;
    border-radius: 20px;
    font-size: 12px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  
  .details {
    border-top: 1px solid #e2e8f0;
    padding-top: 16px;
    margin-bottom: 16px;
  }
  
  .detail-row {
    display: flex;
    justify-content: space-between;
    margin-bottom: 8px;
  }
  
  .label {
    font-size: 12px;
    color: #718096;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  
  .value {
    font-size: 14px;
    color: #2d3748;
    font-weight: 500;
    text-align: right;
  }
  
  .allergeni {
    color: #e53e3e;
    font-weight: 600;
  }

  /* Stili base per tutti i pulsanti */
  .add-to-cart-container {
    margin: 16px 0;
    border-top: 1px solid #e2e8f0;
    padding-top: 16px;
  }

  .add-to-cart-btn {
    width: 100%;
    padding: 12px 16px;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
  }

  .btn-add {
    background-color: #48bb78;
  }

  .btn-add:hover:not(:disabled) {
    background-color: #38a169;
    transform: translateY(-1px);
    box-shadow: 0 2px 8px rgba(72, 187, 120, 0.3);
  }

  /* Stile per il bottone "Rimuovi dal Carrello" (carrello) */
  .btn-remove {
    background-color: #e53e3e;
  }

  .btn-remove:hover:not(:disabled) {
    background-color: #c53030;
    transform: translateY(-1px);
    box-shadow: 0 2px 8px rgba(229, 62, 62, 0.3);
  }

  /* Stile per il bottone di default */
  .btn-default {
    background-color: #4299e1;
  }

  .btn-default:hover:not(:disabled) {
    background-color: #3182ce;
    transform: translateY(-1px);
    box-shadow: 0 2px 8px rgba(66, 153, 225, 0.3);
  }

  /* Stili comuni per tutti i bottoni */
  .add-to-cart-btn:active:not(:disabled) {
    transform: translateY(0);
  }

  .add-to-cart-btn:disabled {
    background-color: #a0aec0 !important;
    cursor: not-allowed;
    color: #718096;
  }

  .add-to-cart-btn:disabled:hover {
    transform: none !important;
    box-shadow: none !important;
  }
  
  .footer {
    border-top: 1px solid #e2e8f0;
    padding-top: 12px;
  }
  
  .supplier {
    color: #718096;
    font-size: 12px;
  }
  
  .text-green-600 { color: #38a169; }
  .bg-green-100 { background-color: #f0fff4; }
  .text-red-600 { color: #e53e3e; }
  .bg-red-100 { background-color: #fed7d7; }
  .text-yellow-600 { color: #d69e2e; }
  .bg-yellow-100 { background-color: #fefcbf; }
  .text-gray-600 { color: #718096; }
  .bg-gray-100 { background-color: #f7fafc; }
</style>