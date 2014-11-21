Tired of executing invidvidual stubbs commands?
Want a single file that specifies the whole module?

This utility reads a rerun module specification defined in XML
and generates all the module code via stubbs command executions.

* Requires: xmlstarlet, rerun


Example module specification file: waitfor.xml

```xml
<?xml version="1.0"?>
<module name="waitfor" version="1.0.1">
  <description>utility commands that wait for a condition.</description>
  <commands>
    <command name="ping">
      <description>wait for ping response from host</description>
      <options>host, interval</options>
      <script><![CDATA[
until ( ping -c 1 $HOST | grep -q ^64 )
do
   sleep $INTERVAL
   echo "Pinging $HOST..."
done
echo "OK: $HOST is pingable."]]></script>
    </command>
  </commands>
  <options>
    <option name="host" required="true" arguments="true">
      <description>seconds between checks</description>
    </option>
    <option name="interval" default="30">
      <description>the host to reach</description>
    </option>
  </options>
</module>
```

After the specification is defined, execute the xml2module utility.
Note, you will need to define RERUN_MODULES environment variable.

```bash
$ export RERUN_MODULES=/path/to/modules
$ xml2module waitfor.xml
waitfor: [OK]
... Adding 1 commands: ping
waitfor:ping [OK]
waitfor:ping --host [OK]
waitfor:ping --interval [OK]
```

Execute a listing:
```
$ rerun waitfor
Available commands in module, "waitfor":
ping: "wait for ping response from host"
    --host <"">: "seconds between checks"
   [ --interval <30>]: "the host to reach"
```
Run the command

```
rerun waitfor:ping --host localhost
OK: localhost is pingable.
```

Try against a host that is coming up:
```
$ rerun waitfor:ping --host my.webservice --interval 2

Pinging my.webservice...
Pinging my.webservice...
OK: my.webservice is pingable.
```
