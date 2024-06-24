<template>
  <div class="flex justify-center items-center p-20">

    <form @submit.prevent="(e) => {
      if (confereInput()) {
        return
      }
      handleSubmit(e)
    }" class="flex flex-col justify-center items-center gap-2">
      <div class="m-5">
        <h1 class="text-[2.5vh]">Editar Corretor</h1>
      </div>
      <div class="text-blue-400">{{ mensagemEditar }}</div>
      <div class="flex space-x-5">
        <div class="">
          <input type="text" name="cpf" placeholder="Digite Seu CPF" class="border-2 w-[20vh] appearance-none" v-model="formulario.cpf" maxlength="11">
          <div class="text-red-300" v-if="erroCPF">CPF deve ter 11 numeros!</div>
          <div class="text-red-300" v-if="erroCPF">CPF é obrigatório</div>
        </div>
        
        <div>
          <input type="text" name="creci" placeholder="Digite Seu Creci" class="border-2 w-[100%]" v-model="formulario.creci">
          <div class="text-red-300" v-if="erroCRECI">Creci deve ter no minimo 2 caracteres</div>
          <div class="text-red-300" v-if="erroCRECI">Numero do Creci é obrigatório</div>
        </div>
      </div>
      <div class="w-[100%] flex flex-col">
        <input type="text" name="name" placeholder="Digite seu nome" class="border-2 w-[100%]" v-model="formulario.name">
        <div class="text-red-300" v-if="erroNOME">Seu nome deve ter no minimo 2 caracteres</div>
        <div class="text-red-300" v-if="erroNOME">Seu nome é obrigatório</div>
      </div>
      <button type="submit" class="w-[100%] bg-slate-700 text-white h-6">Salvar</button>
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
        <div class="flex space-x-5">
          <h2 class="text-slate-300">Excluir</h2>
          <h2 class="text-slate-300">Editar</h2>
        </div>
      </div>
    </div>
  </template>
  
  <script>
    export default {
      data() {
        return {
            corretores: [],
            corretorSelec: '',
            formulario: '',
            idCorretor: this.$route.params.id,
            id: '',
            cpf: '',
            creci: '',
            nome: '',
            erroCPF: false,
            erroCRECI: false,
            erroNOME: false,
            mensagemEditar: '',
        }
    },
    methods: {
    async handleSubmit(e) {
      const formData = new FormData(e.target);
      formData.append("cpf", this.formulario.cpf)
      formData.append("creci", this.formulario.creci)
      formData.append("name", this.formulario.name)
      try {
        const res = await fetch("http://127.0.0.1:8000/api/corretor/" + this.$route.params.id, {
        method: "POST",
        body: formData
      });

      if(res.ok) {
        await new Promise((res) => {setTimeout(() => res('ok'), 3000 )
          this.mensagemEditar = 'Corretor Editado com sucesso!'
        })
          window.location.href = '/';
        }
      }

      catch(err) {
        console.log(err);
      }
    },

    limitaInput() {
      if (this.formulario.cpf.length > 11) {
        this.formulario.cpf = this.formulario.cpf.substring(0, 11);
      }
    },

    confereInput() {
      let controlefunc = false
      if(!this.formulario.cpf || this.formulario.cpf.length < 11) {
        this.erroCPF = true
        controlefunc = true
      }

      if(!this.formulario.creci || this.formulario.cpf.length < 2) {
        this.erroCRECI = true
        controlefunc = true
      }
      
      if(!this.formulario.name || this.formulario.name.length < 2) {
        this.erroNOME = true
        controlefunc = true
      }

      return controlefunc;
    }
  },
    mounted() {
      fetch('http://127.0.0.1:8000/api/corretores')
          .then(api => api.json())
          .then(data => {
        this.corretores = data;
        const corretorSelecionado = data.find(corretor => corretor.id == this.$route.params.id);
        if (corretorSelecionado) {
          this.formulario = { ...corretorSelecionado };
        }
      })
      .catch(error => {
        console.error('Erro ao carregar corretores:', error);
      });
    },
  }
</script>

<style scoped>
</style>
