<template>
<!--just like a form used to enter the info  from user-->
    <div class="add-project container">
     <h2 class="center-align green-text">Add New Project</h2>
     <!--prevent modifier is used to prevent the default action of submit button and directioning it to fire a function on submit-->
     <form @submit.prevent="addProject">
         <div class="field title">
             <label for="title">Project Name</label>
             <input type="text" name="title" v-model="title">
         </div>
         <!--to show the added stack in stacks-->
         <div  class="field" v-for="(stack,index) in stacks" :key="index">
           <!--outputing here the input field so that user can edit it later on-->
           <label for="stack">Stack:</label>  
           <!--In this two way binding value shown is stack with current iterating index-->
           <input type="text" name="stack" v-model="stacks[index]">
           <!--Add a delete button for deleting stack-->
           <i class="material-icons delete" @click="deleteStack(stack)">delete</i>
         </div>

         <div class="field  add-stack">
             <label for="add-stack">Enter the stack</label>
             <!--here user will enter the stack then as two way binded with another by default it is null
              but as user input it will be updated to new value by two way binding -->
             <input type="text" name="add-stack" @keydown.tab.prevent="Adding" v-model="another"/>
         </div>
         <div class="field center-align">
             <!--Showing the feedback msg-->
             <p v-if="feedback" class="red-text">{{feedback}}</p>
             <!--Submit button-->
             <button class="btn pink">ADD PROJECT</button> 

         </div>
     </form>
    </div>
</template>


<script>
import db from "@/Firebase/init"
import slugify from "slugify"
export default {
    name:"AddProject",
    data(){
        return{
            title:null,
            another:null,
            stacks:[],
            feedback:null,
            slug:null

        }
    },
    methods:{
        //function that will be executed at the time of submit event or clicking ADD PROJECT button 
        addProject(){
           
            if(this.title){
                this.feedback=null
                //creating a slug(what we want to slugify, function how we want to slugify)
                this.slug =slugify(this.title,{
                   replacement:"-",
                   remove:/[$*_+`.()'"!\-:@]/g,
                   lower:true
                })

                db.collection("projects").add({
                    title:this.title,
                    stacks:this.stacks,
                    slug:this.slug
                }).then(() => {
                    this.$router.push({name:'Index'})
                }).catch( err => {
                    console.log(err)
                })

            }else{
                this.feedback="You must enter a project name"
            }
        },
        //function executed after keydown event and pressing tab key to store enter data in another and from there to push it into stacks
        Adding(){
            if(this.another){
                this.stacks.push(this.another)
                //we make another null again to make it empty so that we can store information on it again
                this.another=null
                //if after making another empty if we have fill the another then not to show this message by keeping this empty
                this.feedback=null
            }else{
                //to show a warning msg
                this.feedback="You must enter a value to add an stack"
            }
        },
        //deleting the stack
        deleteStack(stack){
            this.stacks=this.stacks.filter(stackElement => {
                return stackElement != stack 
            })
        }
    }
}
</script>

<style >
.add-project{
    margin-top:60px;
    padding: 20px;
    max-width:500px;
}
.add-project h2 {
    font-size:2em;
    margin:20px auto;
}
.add-project .field{
    margin:20px auto;
    position:relative;
}
.add-project .delete {
    position:absolute;
    right:0;
    color:grey;
    font-size: 1.4em;
    cursor: pointer;
}

</style>