---
title: "Sql"
date: 2023-07-28T14:44:59+08:00
tags: [sql]
---


mysql
```
CREATE_INSERT_TRIGGER = """
CREATE TRIGGER tr_b_ins_{0} BEFORE INSERT ON {0} FOR EACH ROW
BEGIN
  IF (NEW.create_at IS NULL)
  THEN
    SET NEW.create_at = UNIX_TIMESTAMP();
  END IF;
  IF (NEW.update_at IS NULL)
  THEN
    SET NEW.update_at = UNIX_TIMESTAMP();
  END IF;
END
"""

CREATE_UPDATE_TRIGGER = """
CREATE TRIGGER tr_b_upd_{0} BEFORE UPDATE ON {0} FOR EACH ROW
BEGIN
  IF (NEW.update_at IS NULL)
  THEN
    SET NEW.update_at = UNIX_TIMESTAMP();
  END IF;
END
"""
```

postgres
```
ts = text('extract(epoch from now())::int')

Column('create_at', Integer, server_default=ts),
Column('update_at', Integer, server_default=ts, server_onupdate=ts)
```

sqlite
```
ts = sa.text("(cast(strftime('%s', 'now') as int))")

Column('create_at', Integer, server_default=ts),
Column('update_at', Integer, server_default=ts, server_onupdate=ts)
```

