---
title: typeorm
date: 2022-01-06 15:07:22
tags:
---

## find relations option

  If there is a foreign key in the entity and you want to get it, then you need to add a relation option.

  ```ts
  const integration = await this.integrationRepository.findOne({
    where: { instagramId },
    relations: ['organization']
  });
  ```

## steps for migration in flicket project

  1. run `yarn typeorm migration:create -n ""`. This will create a blank migration file.
  2. write sql in migration file by yourself
  3. run `yarn typeorm migration:run` to run the migration

  If you want to revert migration, you can run `typeorm migration:revert`. This will revert the latest migration. If you want to revert multiple migrations, then you need to run it multiple times.
