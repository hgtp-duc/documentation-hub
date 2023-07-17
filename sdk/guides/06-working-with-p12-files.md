---
title: Working with p12 files
sidebar_label: Working with p12 files 
slug: /guides/working-with-p12-files
hide_table_of_contents: false
---

#### Finding your Node IDs
Your node ID is the public key of your wallet which will be stored as a p12 file. 

:::caution
If using a Euclid Development Environment project, you must update your configuration to use your own custom p12 files. Projects submitted with the default p12 files that come with the project will be rejected. 

**[How to generate p12 files](/sdk/guides/generating-with-p12-files)**
:::

Download the `cl-wallet.jar` executable. This is distributed as an asset with each [release of Tessellation](https://github.com/Constellation-Labs/tessellation/releases).

Editing the details of the following variables and export to your environment. 
```bash
export CL_KEYSTORE=":your_file_name.p12"
export CL_KEYALIAS=":your_file_alias"
export CL_PASSWORD=":your_file_password"
```

Then you can run the following to get your node ID:
```
java -jar cl-wallet.jar show-id
```