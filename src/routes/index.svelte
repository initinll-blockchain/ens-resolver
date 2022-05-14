<script lang="ts">
    import { onMount } from 'svelte';
	import '../app.css';
	import { ethers } from 'ethers';
	let address: string;
	let ens: string;
	let balance: string;
    let network: string;
    let err:string;

    onMount(async ()=>{
        network = await getNetwork();
    });

    async function getNetwork(): Promise<string> {
        const provider = getProvider();
        if (provider) {            
            return (await provider.getNetwork()).name;
        }
    }

    function getProvider(): ethers.providers.Web3Provider {
        if (window.ethereum) {
            const provider = new ethers.providers.Web3Provider(window.ethereum);
            return provider;
        } else {
            alert("Please Install Metamask");
        }
    }

	async function getEns() {
		try {
            err = "";
            network = await getNetwork();
            const provider = getProvider();
			if (provider) {			                
				if (address) {
					const bal = await provider.getBalance(address);
					balance = ethers.utils.formatEther(bal);
					ens = await provider.lookupAddress(address);
                    
				} else if (ens) {
					const bal = await provider.getBalance(ens);
					balance = ethers.utils.formatEther(bal);
					address = await provider.resolveName(ens);
				} else {
                    alert("Please enter address or ens");
                }

                if (ens) {
                    const resolver = await provider.getResolver(ens);
                    if (resolver) {
                        console.log("Bitcoin Address - ", await resolver.getAddress(0));
                        console.log("Litecoin Address - ", await resolver.getAddress(2));
                        console.log("Dogecoin Address - ", await resolver.getAddress(3));
                        console.log(await resolver.getAvatar());
                        console.log(await resolver.getText("email"));
                        console.log(await resolver.getText("url"));
                        console.log(await resolver.getText("com.twitter"));
                    }                    
                }                
			}
		} catch (error) {
            err = "not found";
			console.log(error);
		}
	}
</script>

<div class="main">
	<div>
		<h1 class="title">Welcome to ens-resolver serrvice !</h1>
		<div class="description">Enter Account address or ENS.</div>
		<div>
			Address :
			<input type="text" bind:value={address} />
		</div>
		<div>
			Ens :
			<input type="text" bind:value={ens} />
		</div>
		<div>
			Balance : {balance !== undefined ? balance : ""}
		</div>
        <div>
            Network : {network !== undefined ? network : ""}
        </div>
        <div>
            Error : {err !== undefined ? err : ""}
        </div>
		<button on:click={getEns}> Click me </button>
	</div>
	<div>
		<img class="image" src="" />
	</div>
</div>

<footer class="footer">Made with &#10084; by LearnWeb3 Punks</footer>
