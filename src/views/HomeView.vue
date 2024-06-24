<template>
  <div class="flex justify-center items-center p-20">

    <form @submit.prevent="(e) => {
      if (confereInput()) {
        return
      }
      handleSubmit(e)
    }" class="flex flex-col justify-center items-center gap-2">
      <div class="m-5">
        <h1 class="text-[2.5vh]">Cadastro de Corretor</h1>
      </div>
      <div>{{ mensagem }}</div>
      <div class="flex space-x-5">
        <div class="">
          <input type="text" name="cpf" placeholder="Digite Seu CPF" class="border-2 w-[20vh] appearance-none" v-model="cpf" maxlength="11">
          <div class="text-red-300" v-if="erroCPF">CPF deve ter 11 numeros!</div>
          <div class="text-red-300" v-if="erroCPF">CPF é obrigatório</div>
        </div>
        
        <div>
          <input type="number" name="creci" placeholder="Digite Seu Creci" class="border-2 w-[100%]" v-model="creci">
          <div class="text-red-300" v-if="erroCRECI">Creci deve ter no minimo 2 caracteres</div>
          <div class="text-red-300" v-if="erroCRECI">Numero do Creci é obrigatório</div>
        </div>
      </div>
      <div class="w-[100%] flex flex-col">
        <input type="text" name="name" placeholder="Digite seu nome" class="border-2 w-[100%]" v-model="nome">
        <div class="text-red-300" v-if="erroNOME">Seu nome deve ter no minimo 2 caracteres</div>
        <div class="text-red-300" v-if="erroNOME">Seu nome é obrigatório</div>
      </div>
      <button type="submit" class="w-[100%] bg-slate-700 text-white h-6">Enviar</button>
    </form>

  </div>
  
  <div class="flex flex-col justify-center items-center">
    <h2 class="mb-10">Carretores Cadastrados</h2>
      <div class="h-5 w-[70%] grid grid-cols-[5%_40%_20%_20%_15%] self-center [&>*]:flex [&>*]:items-center [&>*]:justify-center [&>*]:ring-2 [&>*]:ring-slate-700">
        <p class="">ID</p>
        <p>Nome</p>
        <p>CPF</p>
        <p>Cresci</p>
        <p>Ação</p>
      </div>
      <div v-for="corretor in corretores" :key="corretores.id" class="grid grid-cols-[5%_40%_20%_20%_15%] [&>*]:p-1 [&>*]:ml-2 [&>*]: w-[70%] [&>*]:text-[2vh]">
        <div class="">
          <p>{{ corretor.id }}</p>
        </div>
        <div class="">
          <p class="">{{ corretor.name }}</p>
        </div>
        <div class="">
          <p>{{ corretor.cpf }}</p>
        </div>
        <div class="">
          <p>{{ corretor.creci }}</p>
        </div>
        <div class=" space-x-5">
          <button class="text-red-300" @click="() => excluir(corretor.id)">Excluir</button>
          <router-link :to="`/edit/${corretor.id}`" class="text-blue-300" @click="() => editar(corretor.id, corretor.cpf, corretor.creci, corretor.name)">Editar</router-link>
        </div>
      </div>
    </div>
  </template>
  
  <script>
    export default {
      data() {
        return {
            corretores: [],
            id: '',
            cpf: '',
            creci: '',
            nome: '',
            erroCPF: false,
            erroCRECI: false,
            erroNOME: false,
            mensagem: ''
        }
    },
    methods: {
    async handleSubmit(e) {
      const formData = new FormData(e.target);
      try {
        const res = await fetch("http://127.0.0.1:8000/api/corretor", {
        method: "POST",
        body: formData
      });

      if(res.ok) {
        this.mensagem = 'Cadastro Realizado!'
        await new Promise((res,rej) => {setTimeout(() => res('ok'), 3000 )})
        location.reload(); 
        }
      }
      
      catch(err) {
        console.log(err);
      }
    },

    confereInput() {
      let controlefunc = false
      if(!this.cpf || this.cpf.length < 11) {
        this.erroCPF = true
        controlefunc = true
      }

      if(!this.creci || this.creci.length < 2) {
        this.erroCRECI = true
        controlefunc = true
      }
      
      if(!this.nome || this.nome.length < 2) {
        this.erroNOME = true
        controlefunc = true
      }

      return controlefunc;
    },

    async excluir(id) {
      try {
        const res = await fetch("http://127.0.0.1:8000/api/corretor/" + id, {
        method: "DELETE",
        });
      }

      catch(err) {
        console.log(err);
      }

      location.reload(); 
    }
  },
    mounted() {
      fetch('http://127.0.0.1:8000/api/corretores')
          .then(api => api.json())
          .then(data => { this.corretores = data });
    }
  }
</script>

<style>
input[type=number]::-webkit-outer-spin-button,
input[type=number]::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

input[type=number] {
    -moz-appearance: textfield;
}
</style>