# Sacrosanct files

QRadar apps contain some files that are marked as *sacrosanct*, meaning that they cannot be overwritten or deleted by
apps.

If an app archive contains files at the designated paths the files supplied in the archive are ignored, and the
sacrosanct files remain.

For example, if an app archive contains a file that overwrites `/bin/log_collector.py`, it is ignored, and the
sacrosanct file is kept instead.

> **Tip:** These sacrosanct checks happen when an app archive is extracted. Be careful not to overwrite these
> important files during app installation or runtime.

## Sacrosanct file list

The following files are marked as *sacrosanct*:

- `/bin/log_collector.py`
- `/bin/as_root`
- `/bin/start.sh`
- `/bin/start_flask.sh`
- `/bin/update_ca_bundle.sh`
- `/bin/whitegate_egress.sh`
- `/startup.d/A0.*`
- `/startup.d/A9.*`

This list can be found inside the app container in the `/opt/app-root/sacrosanct_files.txt` file.
