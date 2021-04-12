---
title: Keycloak 源码分析 - 账号拉取
author: moli
published: false
comments: true
date: 2021-04-12 11:30:00
tags:
  - keycloak
  - 源码分析
categories:
  - keycloak
---

### Keycloak 是什么

wiki 百科

> Keycloak 是一个开源软件产品，旨在为现代的应用程序和服务，提供包含身份管理和访问管理功能的单点登录工具。

所以这个工具主要是为了解决账号的同步、身份管理与统一访问下的单点登录工具。

例如你可以将 github、google、facebook 等基于 OAuth2 的账号体系，以及基于 ldap 的账号管理体系集成到 keycloak。

我们并不需要关心账号的来源，只需要与 keycloak 进行通讯，即可解决多平台下不同账号体系的登录问题。

我这下载的版本是 keycloak v12.0.1

#### 公司产品的解决方案

事实上在公司的产品中，我们需要提供类似 keycloak 的能力给客户，但是并不能给我们提供完整的解决方案。

但是致所有要看 keycloak 的源码，主要是有几点期望。

- 了解用户列表的拉取
  - ldap 拉取如何实现大规模用户的拉取
  - 如何合并用户

#### 流程

Keycloak 主要做几件事

1. 将用户拉取下来
2. 将用户合并到系统中
3. 将用户通过协议（例如 OAuth2）暴露出来

因为我们的业务系统中，比较比较重「用户合并」这个能力，但是目前看起来，keycloak 解决的问题跟公司的业务不是很 match。

不过还是大概将 keycloak 的源码大概看了一下。

下面会主要关注 ldap 相关的实现，之所以关注 ldap 主要是因为我们的客户群体用 ldap 的较多，其次是 ldap 的用户量通常比较多，以及具有一定的代表性。

#### LDAP 的用户拉取

> federation/ldap/src/main/java/org/keycloak/storage/ldap/idm/store/IdentityStore.java

```java
public interface IdentityStore {

    LDAPConfig getConfig();

    void add(LDAPObject ldapObject);


    void update(LDAPObject ldapObject);


    void remove(LDAPObject ldapObject);


    public void addMemberToGroup(String groupDn, String memberAttrName, String value);

    public void removeMemberFromGroup(String groupDn, String memberAttrName, String value);


    List<LDAPObject> fetchQueryResults(LDAPQuery LDAPQuery);

    int countQueryResults(LDAPQuery LDAPQuery);

    Set<LDAPCapabilityRepresentation> queryServerCapabilities();

    void validatePassword(LDAPObject user, String password) throws AuthenticationException;


    void updatePassword(LDAPObject user, String password, LDAPOperationDecorator passwordUpdateDecorator);

}
```

上面的代码删除了一些注释，ldap 的查询实现中主要实现了这个接口，诸如 拉取、验证、数量、配置等等。

具体的实现在

> federation/ldap/src/main/java/org/keycloak/storage/ldap/idm/store/ldap/LDAPIdentityStore.java

```java
public class LDAPIdentityStore implements IdentityStore {
 // ……
}
```

然后在 `LDAPQuery` 中会进一步封装调用上面的代码，

> federation/ldap/src/main/java/org/keycloak/storage/ldap/idm/query/internal/LDAPQuery.java

```java
public class LDAPQuery implements AutoCloseable {
}
```

当然，在往上找，就是各种工具类的封装了。

其实本身这些代码没有太多亮点，基本上都是业务上的逻辑（当然可能是我本身 java 接触不多，仅仅是 n 年前写过安卓的水平，海涵）。

#### 用户的合并

keycloak 的用户的合并逻辑其实跟我们公司业务中的用户合并差别很大，几乎不 match……

但是还是看一下，当学习……

用户合并实现了很多的接口。

> federation/ldap/src/main/java/org/keycloak/storage/ldap/LDAPStorageProvider.java

```java
public class LDAPStorageProvider implements UserStorageProvider,
        CredentialInputValidator,
        CredentialInputUpdater.Streams,
        CredentialAuthentication,
        UserLookupProvider.Streams,
        UserRegistrationProvider,
        UserQueryProvider.Streams,
        ImportedUserValidation {
}
```

> federation/ldap/src/main/java/org/keycloak/storage/ldap/LDAPStorageProviderFactory.java

```java
public class LDAPStorageProviderFactory implements UserStorageProviderFactory<LDAPStorageProvider>, ImportSynchronization {

  public SynchronizationResult sync(KeycloakSessionFactory sessionFactory, String realmId, UserStorageProviderModel model) {
  }

  protected SynchronizationResult importLdapUsers(KeycloakSessionFactory sessionFactory, final String realmId, final ComponentModel fedModel, List<LDAPObject> ldapUsers) {
  }

  // ……
}
```

上面的工厂类主要就是同步并导入用户到 keycloak 中。

### 后话

看到 keycloak 其实有地方也挺简单直接的，比如在 ldap 不管用户量的情况下，也是直接 fetch 加载到内存中的。

在我司的业务场景中，合并还是一个比较重的操作，并没有用流式方式来合并处理这些业务，当然跟业务场景有关。

其实 java 的代码比 go 的代码好看很多啊（可能是知名项目本身的质量较高…毕竟红帽出品…），基本上看到其名字就知道其他业务功能。

我司的代码质量还有提升空间～～～
