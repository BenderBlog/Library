<template>
  <div
    v-if="client.isAdmin"
    style="padding-top: 20px; padding: 20px; padding-bottom: 0px"
  >
    <a-typography-title :level="2" class="title">User Control</a-typography-title>
    <div class="batch-box">
      <a-input-search
        style="width: 400px"
        placeholder="Input Username"
        v-model:value="searchString"
        @search="searchx"
      >
        <template #enterButton>
          <a-button type="primary">
            <template #icon> <SearchOutlined /> </template>Search</a-button
          >
        </template>
      </a-input-search>
    </div>
    <div class="batch-box">
      <a-button @click="toRole('staff')" class="batch">To Staff</a-button
      ><a-button @click="toRole('user')" class="batch">To Patron</a-button>
      <a-button @click="toRole('super')" class="batch">To Superuser</a-button>
      <a-button @click="toRole('admin')" class="batch">To Admin</a-button>
      <a-button @click="del(undefined)" class="batch" type="danger">Delete</a-button>
    </div>
    <div style="display: flex; justify-content: center">
      <UserTable
        ref="table"
        :data="data"
        @toRole="(e, f) => toRole(e, f)"
        @delete="(e) => del(e)"
        @changepass="(e, f) => changePass(e, f)"
        :height="height"
        style="max-width: 1000px; width: 100%"
      />
    </div>
  </div>
  <div v-else></div>
</template>
<script setup lang="ts">
import UserTable from "@/components/UserTable.vue";
import { useClient } from "@/stores/client";
import type { BackendResponse, UserDetail, UserTypeResponse } from "@/types/type";
import { SearchOutlined, ToTopOutlined } from "@ant-design/icons-vue";
import { message } from "ant-design-vue";
import type { AxiosResponse } from "axios";
import { useAxios } from "@/stores/axios";
import { computed, onMounted, reactive, ref } from "vue";
import "ant-design-vue/es/message/style/css";
const client = useClient();
const axios = useAxios().Axios;
const searchString = ref("");
const table = ref<typeof UserTable>();
const clearList = computed(() => {
  return table?.value?.clearList;
});
//WARNING: ONLY FOR DEMO! MUST BE REPLACED
const toRole = (role: string, item?: UserDetail) => {
  if (item != undefined) {
    data.value.forEach((e) => {
      //TODO: Finish toRole function
      //For DEMO ONLY!
      if (item === e) {
        //console.log('ready to change...')
        //if (emit('toRole', role, item)) {
        //if (true) {
        //  console.log('success')
        item.accountType = role;
        //} else {
        //  message.error(`Change role for ${item.accountName} failed`)
        //}
      }
    });
  } else {
    table?.value?.currentList.forEach((e: any) => {
      e.accountType = role;
    });
  }
};
//WARNING: ONLY FOR DEMO! MUST BE REPLACED
const del = (item?: UserDetail) => {
  if (item != undefined) {
    data.value.splice(data.value.indexOf(item), 1);
  } else {
    console.log(table?.value?.currentList);
    table?.value?.currentList.forEach((e: any) => {
      data.value.splice(data.value.indexOf(e), 1);
    });
  }
};

//WARNING: ONLY FOR DEMO! MUST BE REPLACED
const changePass = (item: UserDetail, pass: string) => {
  message.info(`Change password successfully`);
};
/*
const toRole = (role: string, item?: UserDetail) => {
  console.log(role)
  console.log(item)
  if (item != undefined) {
    axios
      .post('/ManagerOp/changeAuthority', {
        opUser: client.clientData.clientName,
        account: item.accountName,
        type: role
      })
      .then((e: AxiosResponse<BackendResponse>) => {
        if (e.data.status) {
          return true
        } else {
          throw e.data.msg
        }
      })
      .catch((e: any) => {
        message.info(`Error detected in changing the role: ${e}`)
        return false
      })
  } else {
    table?.value?.currentList.forEach((e: UserDetail) => {
      axios
        .post('/ManagerOp/changeAuthority', {
          opUser: client.clientData.clientName,
          account: e.accountName,
          type: role
        })
        .then((f: AxiosResponse<BackendResponse>) => {
          if (!f.data.status) {
            throw f.data.msg
          }
        })
        .catch((f: any) => {
          message.info(`Error detected in changing the role of ${e.accountName}: ${f}`)
          return false
        })
    })
  }
  //TODO-C: Finish toRole function,更新成功之后刷新页面
  console.log(table?.value?.checkList) //这里是选中的列表;
  client.reload() //这行就是刷新的
}
const del = (item?: UserDetail) => {
  //TODO: Finish del function,成功之后刷新页面
  //Ask api tomorrow

  if (item != undefined) {
    console.log('start del')
    axios
      .post('/ManagerOp/deleteRole', {
        opUser: client.clientData.clientName,
        account: client.clientData.clientName,
        targetAcc: item.accountName
      })
      .then((e: AxiosResponse<BackendResponse>) => {
        if (e.data.status) {
          return true
        } else {
          throw e.data.msg
        }
      })
      .catch((e: any) => {
        message.info(`Error detected in deleting ${item.accountName}: ${e}`)
        return false
      })
  } else {
    table?.value?.currentList.forEach((e: UserDetail) => {
      axios
        .post('/ManagerOp/changeAuthority', {
          opUser: client.clientData.clientName,
          account: client.clientData.clientName,
          type: e.accountName
        })
        .then((f: AxiosResponse<BackendResponse>) => {
          if (!f.data.status) {
            throw f.data.msg
          }
        })
        .catch((f: any) => {
          message.info(`Error detected in deleting ${e.accountName}: ${f}`)
          return false
        })
    })
  }
  client.reload() //这行就是刷新的
}
const changePass = (item: UserDetail, pass: string) => {
  //TODO: change password
  //Ask api tomorrow
  if (item != undefined) {
    console.log('start del')
    axios
      .post('/ManagerOp/changePassword', {
        opUser: client.clientData.clientName,
        account: client.clientData.clientName,
        targetAcc: item.accountName,
        newPassword: 'zfh',
        OpType: 'manager'
      })
      .then((e: AxiosResponse<BackendResponse>) => {
        if (e.data.status) {
          message.info(`Change password successfully`)
          return true
        } else {
          throw e.data.msg
        }
      })
      .catch((e: any) => {
        message.info(`Change password failed`)
        return false
      })
  }
}
*/
//TODO-C: Search formula.
const data = ref<UserDetail[]>([]);
const searchx = () => {
  axios
    .post("SuperuserOp/userInfo", {
      opUser: client.clientData.clientName,
      page: "1",
      num: "999",
    })
    .then((res: AxiosResponse<UserTypeResponse>) => {
      console.log("searchStr" + searchString.value);
      data.value = res.data.accounts.filter(
        (e) => e.accountName.indexOf(searchString.value) != -1
      );
    })
    .catch((e: any) => {
      message.error(`Error while fetching user data: ${e}`);
      data.value = [];
    });
};
searchx();
defineProps<{
  width: number;
  height: number;
}>();
defineExpose({
  clearList,
});
</script>
<style scoped>
.title {
  width: 100%;
  text-align: center;
}
.batch {
  margin-right: 10px;
}
.batch-box {
  display: flex;
  justify-content: center;
  margin-top: 20px;
  margin-bottom: 20px;
}
</style>
