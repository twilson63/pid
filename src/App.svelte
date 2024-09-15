<script>
  import Counter from './counter.svelte'

  //let pid = 'ptCu-Un-3FF8sZ5zNMYg43zRgSYAGVkjz2Lb0HZmx2M'
  let pid = 'xU9zFkq3X2ZQ6olwNVvr1vUWIjc3kXTWr7xKQD6dh10'
  let servers = [] 
  let visible = false
  let isActive = false

  const getSU = (pid) => fetch(`https://su-router.ao-testnet.xyz/${pid}?limit=1000&from=${Date.now() - 60000}`).then(async res => {
      let body = await res.json() 
      let url = res.url
      console.log(body)
      return { body, url }
    }) 
  const getCU = (pid) => fetch(`https://cu.ao-testnet.xyz/dry-run?process-id=${pid}`, {
    method: 'post',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({
      Target: pid,
      Owner: pid, 
      Tags: [
        {name: 'Action', value: 'Info'}
      ]
    })
  }).then(async res => {
      let body = await res.json() 
      let url = res.url

      let cu = (new URL(url)).host.replace('cu', '').replace('.ao-testnet.xyz', '')
      return { body, url, 
        analytics: `https://aocomputer.grafana.net/d/bdnuoox0swohsa/ao-testnet-cus?from=now-6h&to=now&timezone=browser&var-CU_UNITS=${cu}` 
      }
    }) 

  // const getMU = (pid) => fetch(`https://mu.ao-testnet.xyz?debug=true&process=${pid}&message=KNRUNxMG2vOX_oT6FwMOiQDk9zo5xD4EWGPfYm7A7IU&page=1`).then(async res => {
  //     let body = await res.json() 
  //     let url = res.url

  //     return { body, url }
  //   }) 
  
  async function handleClick() {  
    visible = true
    isActive = true
    console.log('Submitted PID:', pid);
    const res = await Promise.all([getSU(pid), getCU(pid)])
    servers = res.map(({url, analytics }) => ({ url: (new URL(url)).origin, analytics } ))
    isActive = false
  }
</script>

<main style="width: 600px;">

  <h1>pid</h1>

  <div class="card">
    <p>Enter pid:</p>
    <input bind:value={pid} type="text" id="pid" name="pid" />
    <button on:click={handleClick}>Find</button>
  </div>
  {#if visible }
    <Counter {isActive} />
  {/if}
  <ol>
    {#each servers as server}
    <li style="text-align:left">
      {server.url}
      {#if server.analytics}
      <a href={server.analytics} target="_blank">Dashboard</a>
      {/if}
    </li>
    {/each}
  </ol>

</main>

<style>
input {
  width: 100%;
  border-radius: 0.375rem;
  border-color: #e5e7eb;
  padding-right: 2.5rem;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  font-size: 0.875rem;
  line-height: 1.25rem;
  padding: .5rem;
  margin-bottom: 1rem;
}
</style>
