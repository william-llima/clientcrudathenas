<template>
<v-card class="mx-auto"
    max-width="600"
    hover>
  <form @submit.prevent="submit">
    <v-row>
      <v-col>
        <v-text-field
      v-model="cpfsearch"
      label="cpf"
    ></v-text-field>
      </v-col>
      <v-col>
      <v-btn 
          class="me-4"
          @click="buscaPessoa">
            pesquisar
          </v-btn>
      </v-col>

    </v-row>
    <v-text-field
      v-model="dataform.nome"
      label="Nome"
    ></v-text-field>

    <v-text-field
      v-model="dataform.data_nasc"
      label="Data de nascimento ex 1/1/2001"
    ></v-text-field>

    <v-text-field
      v-model="dataform.cpf"
      
      label="CPF ex 11111111111"
    ></v-text-field>
    <v-text-field
      v-model="dataform.altura"
      
      label="Altura ex 1.7"
    ></v-text-field>
    <v-text-field
      v-model="dataform.peso"
     
      label="Peso ex 50"
    ></v-text-field>

    <v-select
      v-model="dataform.sexo"
      label="Sexo"
      :items="select"
    ></v-select>
    <v-btn
      class="me-4"
      type="submit"
      v-if='incluir'
      @click="CadastraPessoa"
    >
      Incluir
    </v-btn>

  <template v-if='mostrar'>
    <v-btn 

    class="me-4"
    @click='updatePessoa'
    >
      alterar
    </v-btn>
    
    <v-btn 
    @click="deletePessoa"
    class="me-4">
      excluir
    </v-btn>
    <v-btn 

    class="me-4"
    @click='buscaPesoIdeal'
    >
      PesoIdeal
    </v-btn>
    </template>
    

  </form>
  <v-alert
      v-model="alert"
      border="start"
      close-label="Close Alert"
      :color="coloralert"
       :icon="iconalert"
      :title="textalert"
      variant="tonal"
      closable
    >
    </v-alert>
  </v-card>
</template>
<script >
export default {
  data(){
    return{
      dataform:{
      nome:'',
      data_nasc:'',
      cpf:'',
      altura:'',
      peso:'',
      sexo:'',
      },
      incluir:true,
      mostrar:false,
      cpfsearch:'',
      select:['M','F'],
      alert:false,
      coloralert:'',
      textalert:'',
      iconalert:'',

    }
  },
  methods:{
    CadastraPessoa(){
      let arrvalues=Object.values(this.dataform)
      let valid=true
      arrvalues.forEach(val=>{
        if(val==''){
          valid=false
        }
      })
      if(valid){

        this.dataform.data_nasc = this.dataform.data_nasc.split('/').reverse().join('-')
          
          this.$http.post('/register',this.dataform,{
            headers: {
      "Content-Type": 'application/json'
            }
          }).then(res=>{
            Object.keys(this.dataform).forEach(key=>{
              this.dataform[key] = ''
            })
          if(res.request.status == 201){
            this.mostrar=false,
            this.alert= true
        this.coloralert='success'
        this.iconalert = '$success'
        this.textalert = 'Dados cadastrados com sucesso'
          }
        }
          
        )
      }else{
        this.alert= true
        this.coloralert='warning'
        this.iconalert = '$warning'
        this.textalert = 'Preencha todos os campos'
      }
      
    },
    buscaPessoa(){
      this.$http.get('/search?search='+this.cpfsearch,{
            headers: {
      "Content-Type": 'application/json'
            }
          }).then(res=>{
            
            if(res.data.length > 0){
                res.data[0].data_nasc = res.data[0].data_nasc.split('-').reverse().join('/')
                let data = res.data[0]
                this.dataform = data
                this.mostrar = true
                this.incluir = false
            }else{
              console.log(res)
              this.mostrar = false
            }

          })
    },

    updatePessoa(){


      this.dataform.data_nasc = this.dataform.data_nasc.split('/').reverse().join('-')
      this.$http.put('/pessoas/'+ this.dataform.id +'/update/',this.dataform,{
            headers: {
      "Content-Type": 'application/json'
            }
          }).then(res=>{
            
            if(res.request.status == 200){
                res.data.data_nasc = res.data.data_nasc.split('-').reverse().join('/')
                let data = res.data
                this.dataform = data
                this.mostrar = true
                this.incluir = false
                this.alert = true
                this.textalert = 'Alteração realizada com sucesso'
                this.coloralert = 'success'
                this.iconalert = '$success'
            }else{
              this.mostrar = false
            }

          })
    },

    deletePessoa(){


      this.dataform.data_nasc = this.dataform.data_nasc.split('/').reverse().join('-')
      this.$http.delete('/pessoas/'+ this.dataform.id +'/delete/',{
            headers: {
      "Content-Type": 'application/json'
            }
          }).then(res=>{
            
            if(res.request.status == 204){
              Object.keys(this.dataform).forEach(key=>{
              this.dataform[key] = ''
            })
                this.incluir = true
                this.alert = true
                this.textalert = 'Exclusão realizada com sucesso'
                this.coloralert = 'success'
                this.iconalert = '$success'
            }else{
              this.mostrar = false
            }

          })
    },

    buscaPesoIdeal(){
      let datateste={
        'altura':this.dataform.altura,
        'sexo':this.dataform.sexo,
      }
        this.$http.post('/pessoas/peso',datateste,{
                  headers: {
            "Content-Type": 'application/json'
                  }
                }).then(res=>{
                  this.alert=true
                  this.coloralert = 'success'
                  this.iconalert = '$success'
                  this.textalert = 'Peso Ideal ' + res.data
                }
                )
    }
  }
}  

</script>
