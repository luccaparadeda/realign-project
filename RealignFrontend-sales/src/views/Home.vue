
<script lang="ts" setup>
import { onMounted, ref } from 'vue';
import TrashSVG from '../components/TrashSVG.vue';
export interface User {
  id: string;
  name: string;
  phone: string;
  email: string;
  updatedAt: string;
  createdAt: string;
  called: boolean;
  deleted: boolean;
}
  const users = ref<User[]>([]);

  onMounted(() => {
    fetch('http://localhost:3000/dev/getUsers')
      .then((res) => res.json())
      .then((data) => {
        users.value = data;
      });

      
    });

    function updateUser(userId: string, isCalled: boolean) {
      const body: Partial<User> = {
        called: !isCalled,
      }
      fetch(`http://localhost:3000/dev/updateUser/${userId}`, {
        method: 'PUT',
        body: JSON.stringify(body),
      })
        .then((res) => res.json())
        .then((data) => {
          users.value = users.value.map((user) => {
            if (user.id === userId) {
              return data;
            }
            return user;
          });
        });
    }

    function deleteUser(userId: string) {
      const body: Partial<User> = {
        deleted: true,
      }
      fetch(`http://localhost:3000/dev/updateUser/${userId}`, {
        method: 'PUT',
        body: JSON.stringify(body),
      })
        .then((res) => res.json())
        .then((data) => {
          users.value = users.value.filter((user) => user.id !== userId);
        });
    }
</script>


<template>
  
  <div class="tableWrapper">
    <v-table class="v-table">
       <thead>
         <tr>
           <th class="text-left">
             Phone
           </th>
           <th class="text-left">
             Email
           </th>
           <th class="text-left">
             Status
           </th>
           <th class="text-left">
             Change Status
           </th>
           <th class="text-left">
             Delete user
           </th>
         </tr>
       </thead>
       <tbody>
         <tr
         v-for="user in users"
         :key="user.id"
         >
         <td class="phoneNumber">{{ user.phone }}</td>
         <td class="email">{{ user.email }}</td>
         <td v-if="user.called" class="alreadyCalled">already called</td>
         <td v-else class="notCalled">not called</td>
         <td> <v-btn  @click="updateUser(user.id, user.called)"> Change</v-btn></td>
         <td> <v-btn  @click="deleteUser(user.id)"> <TrashSVG/></v-btn></td>
        </tr>
      </tbody>
    </v-table>
  </div>
</template>


<style scoped>

  .tableWrapper {
    width: 90%;
    margin: 0 auto;
  }

  .v-table {
    overflow-x: scroll !important;
  }
  .phoneNumber {
    letter-spacing: 1.2px;
  }

  .notCalled {
    font-weight: 600;
    color: #f44336;
  }

  .alreadyCalled {
    font-weight: 600;
    color: #4caf50;
  }
</style>
