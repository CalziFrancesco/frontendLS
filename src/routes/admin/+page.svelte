<script>
  import { onMount } from 'svelte';
  
  let articoli = $state([]);
  let categoria = $state('');
  let errore = $state('');
  let nuovoArticolo = $state({
    Id_art: '',
    nome_prodotto: '',
    descrizione_prodotto: '',
    categoria: '',
    marca: '',
    prezzo: '',
    quantità: '',
    stato: 'disponibile',
    sconto: '0%',
    origine: '',
    urlImg: '',
    fornitore: '',
    allergeni: '',
    ingredienti: '',
    rating: '',
    scaffale: '',
    corsia: ''
  });

  async function caricaArticoli() {
    try {
      const res = await fetch('https://backendls-1.onrender.com/articoli',
                {credentials: 'include'});
      const data = await res.json();
      articoli = data;
    } catch (e) {
      errore = 'Errore nel caricamento articoli';
    }
  }

  async function filtraCategoria() {
    try {
      const res = await fetch(`http://localhost:3080/articoli/categoria/${categoria}`,
                {credentials: 'include'});
      const data = await res.json();
      articoli = data;
    } catch (e) {
      errore = 'Errore nel filtro per categoria';
    }
  }

  async function aggiungiArticolo() {
    try {
      const res = await fetch('http://localhost:3080/articoli', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(nuovoArticolo),
        credentials: 'include'
      });
      if (!res.ok) throw new Error();
      await caricaArticoli();
      nuovoArticolo = {
        Id_art: '',
        nome_prodotto: '',
        descrizione_prodotto: '',
        categoria: '',
        marca: '',
        prezzo: '',
        quantità: '',
        stato: 'disponibile',
        sconto: '0%',
        origine: '',
        urlImg: '',
        fornitore: '',
        allergeni: '',
        ingredienti: '',
        rating: '',
        scaffale: '',
        corsia: ''
      };
    } catch (e) {
      errore = 'Errore nell\'aggiunta articolo';
    }
  }

  async function eliminaArticolo(id) {
    try {
      const risposta = await fetch(`http://localhost:3080/articoli/${id}`, {
        method: 'DELETE',
        credentials: 'include'
      });
      if (risposta.ok) {
        const dati = await risposta.json();
        console.log('✅ Articolo eliminato:', dati.message);
        await caricaArticoli();
        errore = '';
      } else {
        const erroreResp = await risposta.json();
        console.error('❌ Errore:', erroreResp.message);
        errore = 'Errore nell\'eliminazione dell\'articolo';
      }
    } catch (err) {
      console.error('Errore di rete o del server:', err);
      errore = 'Errore di rete nell\'eliminazione';
    }
  }

  onMount(caricaArticoli);
</script>

<h1 class="text-xl font-bold mb-4">Pannello Admin - Articoli</h1>

{#if errore}
  <p class="text-red-600">{errore}</p>
{/if}

<div class="mb-4">
  <input type="text" bind:value={categoria} placeholder="Filtra per categoria" class="border p-1" />
  <button onclick={filtraCategoria} class="bg-blue-500 text-white px-2 py-1 ml-2">Filtra</button>
</div>

<table class="table-auto w-full text-left border">
  <thead>
    <tr>
      <th>ID</th>
      <th>Nome</th>
      <th>Categoria</th>
      <th>Marca</th>
      <th>Prezzo</th>
      <th>Quantità</th>
      <th>Stato</th>
      <th>Corsia</th>
      <th>Scaffale</th>
      <th>Azioni</th>
    </tr>
  </thead>
  <tbody>
    {#each articoli as articolo}
      <tr>
        <td>{articolo.Id_art}</td>
        <td>{articolo.nome_prodotto}</td>
        <td>{articolo.categoria}</td>
        <td>{articolo.marca}</td>
        <td>{articolo.prezzo}</td>
        <td>{articolo.quantità}</td>
        <td>{articolo.stato}</td>
        <td>{articolo.corsia}</td>
        <td>{articolo.scaffale}</td>
        <td>
          <button 
            onclick={() => eliminaArticolo(articolo.Id_art)} 
            class="bg-red-500 text-white px-2 py-1 text-sm hover:bg-red-600"
          >
            Rimuovi
          </button>
        </td>
      </tr>
    {/each}
  </tbody>
</table>

<h2 class="mt-6 text-lg font-semibold">Aggiungi nuovo articolo</h2>
<div class="space-y-2">
  <input type="number"  bind:value={nuovoArticolo.Id_art} placeholder="ID Articolo" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.nome_prodotto} placeholder="Nome prodotto" class="border p-1 w-full" />
  <textarea bind:value={nuovoArticolo.descrizione_prodotto} placeholder="Descrizione prodotto" class="border p-1 w-full h-20"></textarea>
  <input type="text" bind:value={nuovoArticolo.categoria} placeholder="Categoria" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.marca} placeholder="Marca" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.prezzo} placeholder="Prezzo (es. 12.50$)" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.quantità} placeholder="Quantità (es. 500g)" class="border p-1 w-full" />
  <select bind:value={nuovoArticolo.stato} class="border p-1 w-full">
    <option value="disponibile">Disponibile</option>
    <option value="esaurito">Esaurito</option>
  </select>
  <input type="text" bind:value={nuovoArticolo.sconto} placeholder="Sconto (es. 10%)" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.origine} placeholder="Origine" class="border p-1 w-full" />
  <input type="url" bind:value={nuovoArticolo.urlImg} placeholder="URL Immagine" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.fornitore} placeholder="Fornitore" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.allergeni} placeholder="Allergeni" class="border p-1 w-full" />
  <textarea bind:value={nuovoArticolo.ingredienti} placeholder="Ingredienti" class="border p-1 w-full h-16"></textarea>
  <input type="text" bind:value={nuovoArticolo.rating} placeholder="Rating (es. 4/5)" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.scaffale} placeholder="Scaffale" class="border p-1 w-full" />
  <input type="text" bind:value={nuovoArticolo.corsia} placeholder="Corsia" class="border p-1 w-full" />
  <button onclick={aggiungiArticolo} class="bg-green-500 text-white px-4 py-1">Aggiungi</button>
</div>

<style>
  input {
    display: block;
  }
</style>