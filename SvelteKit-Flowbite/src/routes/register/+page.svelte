<script>
    import { Card, Img, GradientButton, Toggle, Label, Input, TextPlaceholder } from "flowbite-svelte";
    import { ArrowRightOutline } from 'flowbite-svelte-icons';
    import axios from 'axios';
    import {PUBLIC_APIURL} from '$env/static/public';
   let users = {fname:"",lname:"",email:"", password:""};
    async function register(){
        const apiurl = PUBLIC_APIURL;
        try{
            const response = await fetch(`${apiurl}/users/register`,{
                method:'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ "first_name":users.fname, "last_name":users.lname, "email":users.email, "password":users.password }),
            });
            if (response.ok) {
            console.log('Registration successful');
            alert(`Registration Successful`);
            window.location='/';
            } 
            else {
                console.error('Error:');
            }
        }
        catch(error){
            console.error(`Could not register:`,error);
        }

    }
</script>
<body background='rg-bg.png' style='background-size:80%; background-position: right; background-repeat:no-repeat;'>
<div  class="space-y-2" style="height:1000px; width:450px;">
    <Card class='!max-w-full h-full'>
        <div>    
        <Img size='w-14' src='icon.png' alt='todo icon' style='float:left;'/>
        <span class='mt-3 text-2xl font-bold tracking-tight text-gray-900 dark:text-white' style="float:inline-start; margin-left:10px;">To-Do</span>
        </div>
        <h5 class= 'mt-20 mb-5 text-3xl font-bold tracking-tight text-gray-900 dark:text-white'>Register</h5>
        <form>
            <Label class='mb-2 space-y-2'><span class='text-lg'>First Name</span>
                <Input bind:value={users.fname} type='text' placeholder='First Name' required></Input>
            </Label>
            <Label class='mb-2 space-y-2'><span class='text-lg'>Last Name</span>
                <Input bind:value={users.lname} type='text' placeholder='Last Name' required></Input>
            </Label>
            <Label class='mb-2 space-y-2'><span class='text-lg'>Email</span>
                <Input bind:value={users.email} type='text' placeholder='abc@example.com' required></Input>
            </Label>
            <Label class='mb-2 space-y-2'><span class='text-lg'>Password</span>
                <Input bind:value={users.password} type='text' placeholder='***********' required></Input>
            </Label>
        <GradientButton on:click={register} color='blue' class='mt-4'>
        Submit <ArrowRightOutline class="w-6 h-6 ms-2 text-white" />
        </GradientButton>
        </form>
    </Card>
</div>
</body>
