# Puma Refork Issue with systemd

This repository is a minimal reproduction of the issue described in [puma/puma#3273](https://github.com/puma/puma/issues/3273).

## Steps to reproduce

1. Create a new systemd service with the [puma.service](puma.service) file. To do so, copy the file to `/etc/systemd/system/puma.service` and run `systemctl daemon-reload`.
2. Copy the [puma.rb](puma.rb) and [hello.ru](hello.ru) file to `/tmp`.
3. Ensure that the `puma` gem is installed through `bundle install`
4. Run `systemctl start puma.service`.
5. See that the service is running through `systemctl status puma.service`. Everything is fine.
6. Now, run `systemctl reload puma.service`. This will send a `SIGURG` signal to the puma process, which should cause it to refork.
7. See that the service is now in a `reloading (reload)` state through `systemctl status puma.service`. This state won't be left until puma is restarted through `systemctl restart puma.service`.

## Things to adjust

- The `WorkingDirectory` setting in the [puma.service](puma.service) file. Currently, it is expected that the `puma.rb` and `hello.ru` files are located in `/tmp`.
- The `ExecStart` setting in the [puma.service](puma.service) file, in order to point to `bundle` in the correct location. Currently, it is expected that `bundle` is located in `/usr/local/bin/bundle`.
