<script setup>
import { ref , reactive, watch, onMounted} from 'vue'
import { uid } from 'uid'
import Header from './components/Header.vue'
import Formulario from './components/Formulario.vue'
import Paciente from './components/Paciente.vue'

const pacientes = ref([])

const paciente = reactive({
    id: null,
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas: ''
})

watch(pacientes, ()=>{
  guardarLocalStorage()
},{
  deep: true,
})

const guardarLocalStorage = () =>{
  localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
}

onMounted(()=>{
  const pacientesStorage = localStorage.getItem('pacientes', JSON.stringify('pacientes'))
  if (pacientesStorage) {
    pacientes.value = JSON.parse(pacientesStorage)//convertir de string a arreglo
  }
})

const guardarPaciente = () =>{
  if(paciente.id){
    const { id } = paciente
    const i = pacientes.value.findIndex(paciente => paciente.id === id)
    pacientes.value[i] = {...paciente}
  }else{
    pacientes.value.push({
    ...paciente, //le quitamos la reactividad para que los cambios se conserven en la vista
    id: uid()
  })
  }
  

  //reiniciar el objeto
  // paciente.nombre = ''...

  //otra forma
  Object.assign(paciente,{
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas: '',
    id: null
  })
}


const actualizarPaciente = (id) =>{
  const pacienteEditar = pacientes.value.filter(paciente => paciente.id == id)[0]
  Object.assign(paciente, pacienteEditar)
}

const eliminarPaciente = (id) =>{
  pacientes.value = pacientes.value.filter(paciente => paciente.id !== id)
}

</script>

<template>
  <div class="container mx-auto mt-20">
    <Header />

    <div class="mt-12 md:flex">
      <Formulario 
          v-model:nombre="paciente.nombre"
          v-model:propietario="paciente.propietario"
          v-model:email="paciente.email"
          v-model:alta="paciente.alta"
          v-model:sintomas="paciente.sintomas"
          @guardar-paciente="guardarPaciente"
          :id="paciente.id"
      />

      <div class="md:w-1/2 md:h-screen overflow-y-scroll ">
        <h1 class="font-black text-3xl text-center">Administra tus pacientes </h1>
        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Informacion de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>

          <Paciente
            v-for="paciente in pacientes"
            :paciente="paciente"
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente"
            :key="paciente.id"
          />
              
        </div>
        <p v-else class="mt-10 text-center text-2xl">No hay pacientes</p>
      </div>
    </div>
  </div>
</template>


