<template>
  <div id="home">

    <Card :bordered="false">
      <p slot="title">ProLease SAML Service Provider File Generator</p>
      <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="120">
        <FormItem label="Account Directory" prop="accountDirectory">
          <Input v-model="formValidate.accountDirectory" placeholder="PL_XYZCompany" />
        </FormItem>
        <FormItem label="Expiration Date" prop="expirationDate">
          <Row type="flex" justify="start">
            <DatePicker v-model="formValidate.expirationDate" type="date" placeholder="2020-12-31"></DatePicker>
          </Row>
        </FormItem>
        <FormItem label="Logout URL" prop="logoutUrl">
          <Input v-model="formValidate.logoutUrl" placeholder="http://www.mycompany.com/saml/logout.html" />
        </FormItem>
        <FormItem label="X509 Certificate" prop="certificate">
          <Input v-model="formValidate.certificate" type="textarea" :rows="20" />
        </FormItem>
        <FormItem>
          <Button type="primary" @click="handleSubmit('formValidate')">Generate SAML</Button>
          <Button type="ghost" @click="handleReset('formValidate')" style="margin-left: 8px">Clear All</Button>
        </FormItem>
      </Form>
    </Card>

  </div>
</template>

<script>
  import fileSaver from 'file-saver';
  import moment from 'moment';

  export default {
    name: 'home',
    data() {
      return {
        formValidate: {
          accountDirectory: '',
          expirationDate: moment().add(5, 'years').toDate(),
          logoutUrl: '',
          certificate: '',
        },
        ruleValidate: {
          accountDirectory: [
            { required: true, message: 'Account Directory cannot be blank', trigger: 'blur' },
          ],
          expirationDate: [
            { required: true, type: 'date', message: 'Expiration Date cannot be blank', trigger: 'change' },
          ],
          certificate: [
            { required: true, message: 'X509 Certificate cannot be blank', trigger: 'blur' },
          ],
        },
      };
    },
    methods: {
      handleSubmit(name) {
        this.$refs[name].validate((valid) => {
          if (valid) {
            const blob = new Blob(['Hello, world!'], { type: 'text/plain;charset=utf-8' });
            fileSaver.saveAs(blob, 'hello world.txt');
            this.$Message.success('Generated SAML Service Provider file!');
          } else {
            this.$Message.error('Please make sure to fill all required fields!');
          }
        });
      },
      handleReset(name) {
        this.$refs[name].resetFields();
      },
    },
    components: {
      fileSaver,
      moment,
    },
  };
</script>

<style lang="scss" scoped>
  #home{
    width: 700px;
  }

</style>
