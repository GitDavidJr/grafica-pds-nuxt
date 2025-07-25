<template>
  <Dialog v-model:open="open">
    <DialogTrigger as-child>
      <Button> Cadastrar cliente </Button>
    </DialogTrigger>

    <DialogContent class="sm:max-w-[425px]">
      <DialogHeader>
        <DialogTitle>Cadastrar Cliente</DialogTitle>
        <DialogDescription>
          Preencha os campos abaixo para cadastrar um novo cliente.
        </DialogDescription>
      </DialogHeader>

      <div class="grid gap-4 py-4">
        <!-- NOME -->
        <div class="grid grid-cols-4 items-center gap-4">
          <Label for="nome" class="text-right">Nome</Label>
          <Input
            id="nome"
            v-model="nome"
            class="col-span-3"
            placeholder="Digite o nome do cliente"
          />
        </div>

        <div class="grid grid-cols-4 items-center gap-4">
          <Label for="nome" class="text-right">Endereço</Label>
          <Input
            id="endereco"
            v-model="endereco"
            class="col-span-3"
            placeholder="Av Principal, 10 - Bairro Novo."
          />
        </div>

        <!-- CPF -->
        <div class="grid grid-cols-4 items-center gap-4">
          <Label for="cpf" class="text-right">CPF</Label>
          <Input
            id="cpf"
            v-model="cpf"
            v-mask="'###.###.###-##'"
            class="col-span-3"
            placeholder="000.000.000-00"
            maxlength="14"
          />
        </div>

        <!-- TELEFONE -->
        <div class="grid grid-cols-4 items-center gap-4">
          <Label for="telefone" class="text-right">Telefone</Label>
          <Input
            id="telefone"
            v-model="telefone"
            v-mask="['(##) ####-####', '(##) #####-####']"
            class="col-span-3"
            placeholder="(00) 00000-0000"
            maxlength="15"
          />
        </div>

        <!-- EMAIL -->
        <div class="grid grid-cols-4 items-center gap-4">
          <Label for="email" class="text-right">Email</Label>
          <Input
            id="email"
            v-model="email"
            class="col-span-3"
            placeholder="exemplo@email.com"
          />
        </div>
      </div>

      <DialogFooter>
        <Button type="submit" @click="createCliente">Criar Cliente</Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useToast } from "@/components/ui/toast";
import {
  Dialog,
  DialogTrigger,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
} from "@/components/ui/dialog";
import { Label } from "@/components/ui/label";

const nome = ref("");
const cpf = ref("");
const telefone = ref("");
const email = ref("");
const endereco = ref("");
const open = ref(false);
const error = ref<string>();
const { toast } = useToast();
const emit = defineEmits(["clienteCriado"]);

// Validações individuais
function validaNome() {
  if (nome.value.trim().length < 3) {
    error.value = "Nome muito curto. O nome precisa ter no mínimo 3 caracteres.";
    return false;
  }
  return true;
}

function validaEndereco() {
  if (endereco.value.trim().length < 5) {
    error.value = "Endereço muito curto. O endereço precisa ter no mínimo 5 caracteres.";
    return false;
  }
  return true;
}

function validaCPF() {

  return true;
}

function validaTelefone() {

  return true;
}

function validaEmail() {
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) {
    error.value = "E-mail inválido.";
    return false;
  }
  return true;
}

const limparNumero = (valor: string) => valor.replace(/\D/g, "");

const createCliente = async () => {
  if (
    !validaNome() ||
    !validaEndereco() ||
    !validaCPF() ||
    !validaTelefone() ||
    !validaEmail()
  ) {
    toast({
      title: "Erro no formulário",
      description: error.value,
      variant: "destructive",
    });
    return;
  }

  try {
    await useFetch("/api/clientes/create", {
      method: "POST",
      body: {
        nome: nome.value,
        endereco: endereco.value,
        cpf: limparNumero(cpf.value),
        telefone: limparNumero(telefone.value),
        email: email.value,
      },
    });

    toast({
      title: "Cliente cadastrado!",
      description: "O cliente foi criado com sucesso.",
    });

    emit("clienteCriado");

    nome.value = "";
    cpf.value = "";
    telefone.value = "";
    endereco.value = "";
    email.value = "";
    error.value = undefined;
    open.value = false;
  } catch (error) {
    toast({
      title: "Erro ao criar cliente",
      description: error instanceof Error ? error.message : "Erro inesperado.",
      variant: "destructive",
    });
    console.error("Erro ao criar cliente:", error);
  }
};
</script>
