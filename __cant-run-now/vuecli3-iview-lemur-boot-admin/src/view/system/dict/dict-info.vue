<template>
  <Modal v-model="isShow" width="65%" :closable="false" :mask-closable="false">
    <div slot="header" class="info-header">
      <div class="ivu-modal-header-inner">字典管理</div>
      <a class="ivu-modal-close" @click="closeModel"><i class="ivu-icon ivu-icon-ios-close"></i></a>
    </div>
    <div>
      <Form ref="dictForm" :model="form" :label-width="80" :rules="ruleValidate">
        <Row>
          <Col span="8">
            <FormItem label="Key" prop="key">
              <Input v-model="form.key" placeholder="user.sex"></Input>
            </FormItem>
          </Col>
          <Col span="8">
            <FormItem label="名称" prop="name">
              <Input v-model="form.name" placeholder="性别"></Input>
            </FormItem>
          </Col>
          <Col span="8">
            <FormItem label="备注">
              <Input v-model="form.tips"></Input>
            </FormItem>
          </Col>
        </Row>
        <Divider orientation="left">字典值</Divider>
        <Row v-for="dict in subDict" :key="dict">
          <Col span="2" width="60" style="padding-left: 20px">
            <Icon type="ios-add-circle-outline" size="24" @click="addSubDict(dict)"/>
            <Icon type="ios-remove-circle-outline" size="24" @click="removeSubDict(dict)"/>
          </Col>
          <Col span="6">
            <FormItem label="Key">
              <Input v-model="dict.key" placeholder="user.sex"></Input>
            </FormItem>
          </Col>
          <Col span="6">
            <FormItem label="名称">
              <Input v-model="dict.name" placeholder="性别"></Input>
            </FormItem>
          </Col>
          <Col span="6">
            <FormItem label="备注">
              <Input v-model="dict.tips"></Input>
            </FormItem>
          </Col>
        </Row>
      </Form>
    </div>
    <div slot="footer">
      <Button @click="closeModel">取消</Button>
      <Button type="primary" @click="submit" :disabled="submitIsDisabled">提交</Button>
    </div>
  </Modal>
</template>

<script>

import { arrRemove } from '@/libs/tools'
import { C, U } from '@/libs/api.request'
import { Message } from 'iview'

export default {
  name: 'dict-info',
  data () {
    return {
      form: {},
      subDict: [{
        key: '',
        name: '',
        tipe: ''
      }],
      isShow: false,
      type: 'add',
      ruleValidate: {
        key: [
          { required: true, message: 'Key不允许为空', trigger: 'blur' }
        ],
        name: [
          { required: true, message: '字典名称不允许为空', trigger: 'blur' }
        ]
      },
      submitIsDisabled: false
    }
  },
  methods: {
    openModel (type, data) {
      this.isShow = true
      this.submitIsDisabled = false
      this.type = type
      if (data) {
        this.form = data
        this.subDict = data.subDict
      } else {
        this.form = {}
        this.subDict = [{
          key: '',
          name: '',
          tipe: ''
        }]
      }
    },
    closeModel () {
      this.isShow = false
    },
    addSubDict () {
      this.subDict.push({
        key: '',
        name: '',
        tipe: ''
      })
    },
    removeSubDict (dict) {
      if (this.subDict.length > 1) {
        this.subDict = arrRemove(dict, this.subDict)
      }
    },
    submit () {
      this.submitIsDisabled = true
      var isValid = true
      this.subDict.forEach(function (data) {
        if (!data.key || !data.name) {
          Message.warning('数据不允许为空')
          isValid = false
        }
      })
      if (!isValid) {
        this.submitIsDisabled = false
        return
      }
      this.form.subDict = this.subDict
      this.$refs.dictForm.validate((valid) => {
        if (valid) {
          switch (this.type) {
            case 'create':
              C('dict', this.form).then(data => {
                this.isShow = false
                this.$emit('handleSearch')
              })
              break
            case 'update':
              U('dict', this.form).then(data => {
                this.$emit('handleSearch')
                this.isShow = false
              })
              break
          }
        } else {
          this.$Message.error('请检查填写的数据')
        }
      })
    }
  }
}
</script>

<style scoped>
  .info-header {
    height: 40px;
    color: #31708f;
    background-color: #d9edf7;
    border-color: #bce8f1;
  }

  .ivu-modal-header-inner {
    margin: 10px 15px 0px 0px;
    padding-left: 15px;
    height: 40px;
  }

  .ivu-modal-close {
    margin: 10px 15px 0px 0px;
  }

  .ivu-form-item {
    width: 260px;
  }
</style>
