<script>
    import axios from "axios"
    import { navigate } from "svelte-routing";
    let api_url = "http://localhost:5000/auth"
    let email_or_username = ""
    let password = ""
    let username = ""
    let action = "Register" 
    let alt = "Login"
    let loading = false
    let error = false
    let error_message = ""
    function authenticate(){
        loading = true
        error = false
        let endoint = action.toLowerCase()
        axios.post(
            `${api_url}/${endoint}`,
            {
                email_or_username,
                email: email_or_username,
                username,
                password
            }
        ).then(res=>{
            loading = false
            if(res.status===200){
                localStorage.setItem("user", res.data.user)
                navigate("/")
            }
        }).catch(err=>{
            loading = false
            error = true
            switch (err.response.status) {
                case 400:
                    error_message="Wrong password"
                    break;
                case 404:
                    error_message="User not found"
                    break
                case 409:
                    error_message=err.response.data.message
                    break
                case 500:
                    error_message="Something went wrong. Please Try again"
                    break
            }
        })
    }
    function switch_action(){
        if(action==="Login"){
            action="Register"
            alt="Login"
            return
        }
        action="Login"
        alt="Register"
    }
</script>

<main class="  flex flex-col items-center justify-center h-screen" >
    <form class="flex flex-col gap-3" on:submit|preventDefault={authenticate} >
        <input name="email_or_username" bind:value={email_or_username} class="input" required type="text" placeholder={`Email ${action==="Login"?"or username":""}`}>
        <input name="username" bind:value={username} hidden={action==="Login"} class="input " type="text" placeholder="Username">
        <input name="password" bind:value={password} class="input" type="password" required placeholder="Password">
        <span hidden={!error} class=" text-xs text-red-600 " >{error_message}</span>
        <button type="submit" class="btn-v2 " >
            {#if !loading}
                <h1>
                    {action}
                </h1>
            {:else}
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5 animate-spin">
                    <path fill-rule="evenodd" d="M15.312 11.424a5.5 5.5 0 01-9.201 2.466l-.312-.311h2.433a.75.75 0 000-1.5H3.989a.75.75 0 00-.75.75v4.242a.75.75 0 001.5 0v-2.43l.31.31a7 7 0 0011.712-3.138.75.75 0 00-1.449-.39zm1.23-3.723a.75.75 0 00.219-.53V2.929a.75.75 0 00-1.5 0V5.36l-.31-.31A7 7 0 003.239 8.188a.75.75 0 101.448.389A5.5 5.5 0 0113.89 6.11l.311.31h-2.432a.75.75 0 000 1.5h4.243a.75.75 0 00.53-.219z" clip-rule="evenodd" />
                </svg>
            {/if}
        </button>
        <button type="button" class=" text-gray-500 underline" on:click={switch_action}>
            <h1>
                {alt} instead
            </h1>
        </button>
    </form>
</main>

