# Kubernetesとは


# Kubernetes環境

Kubernetesは環境に応じたディストリビューションを使用します。

## Minikube

ローカルでKubernetesを使用するためのディストリビューションです。
冗長性は担保されず、本番向きではない。

## Docker Desktop


## MicroK8s

UbuntuのCanonicalが提供するKubernetesパッケージ。

## OpenShift

Kubernetesは開発者を多く抱えており、発展速度が速い。
OpenShiftは、Red Hat社がエンタープライズ向けにカスタマイズされたもので、CIなどKubernetesには含まれない高度な機能も統合されている。

# 概念

## pod

podはアプリケーション固有の論理ホストをモデル化したもので、Kubernetesプラットフォーム上の原子単位。
podにはコンテナを複数含めることができる。

## マニフェスト

リソースや使用するコンテナを定義する。
管理方法は大まかに分けて、次がある。

### モノレポジトリ

- アプリケーションとマニフェストをひとつのリポジトリで管理する
- アプリケーションの対してトリガーできる

### マルチレポジトリ

- アプリケーションとマニフェストを異なるリポジトリで管理する
- アプリケーションの変更と異なるトリガーで実行できる
