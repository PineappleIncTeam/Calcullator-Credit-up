
import me.leqada.rest.service.Calculator;
import org.apache.log4j.Logger;
import javax.ws.rs.ApplicationPath;
import java.util.HashSet;
import java.util.Set;

@ApplicationPath("/")
public class Application extends javax.ws.rs.core.Application {
private final static Logger logger = Logger.getLogger(Application.class);

public Set<Class> getClasses() { final Set> resources = new HashSet<>();

try {
resources.add(Calculator.class);
}
catch(Exception e) {
logger.error(e.getStackTrace());
}
return resources;
}
me.leqada.rest
├── Application.java
├── logic
│ ├── Cache.java
│ └── Logic.java
├── model
│ ├── ActInfo.java
│ ├── Error.java
│ └── Output.java
└── service
└── Calculator.java
Applicationclass (только чтобы не использовать web.xml):
import me.leqada.rest.service.Calculator;
import org.apache.log4j.Logger;
import javax.ws.rs.ApplicationPath;
import java.util.HashSet;
import java.util.Set;
@ApplicationPath("/")
public class Application extends javax.ws.rs.core.Application {
private final static Logger logger = Logger.getLogger(Application.class);
public Set<Class> getClasses() { final Set> resources = new HashSet<>();
try {
resources.add(Calculator.class);
}
catch(Exception e) {
logger.error(e.getStackTrace());
}
return resources;
}
}
Calculatorкласс (обработчики запросов):
import me.leqada.rest.logic.Cache;
import me.leqada.rest.logic.Logic;
import me.leqada.rest.model.ActInfo;
import me.leqada.rest.model.Output;
import org.apache.log4j.Logger;
import me.leqada.rest.model.Error;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.Response;
@path("/")
@produces("application/json")
public class Calcullator {
private final static Logger logger = Logger.getLogger(Calculator.class);
private Output output;
private ActInfo resultInfo = new ActInfo();
private final Error error = new Error();
private final Logic logic = new Logic();
@get
@path("multiply/{a}/{b}/{c}")
public Response multiplication(
@PathParam("a") String textA,
@PathParam("b") String textB,
@PathParam("c") String textC) {
logger.info("Requested multiplication: " + textA + " " + textB + " " + textC);
String[] paramsText = {"multiplication", textA, textB, textC};
// Trying to get result from cache
// If there is no result for this parameters
// then we need to calculate it
resultInfo = Cache.get(paramsText, logic);
// If error code = 0 then there is no problem with params
if (resultInfo.getErrorCode() == 0) {
logger.info("Returning: " + resultInfo.getResult());
output = new Output("multiplication", resultInfo.getResult());
return Response.status(200).entity(output).build();
}
else {
logger.info("Returning Error: " + resultInfo.getErrorCode() + "|" + resultInfo.getErrorText());
error.setError(resultInfo.getErrorCode(), resultInfo.getErrorText());
return Response.status(400).entity(error).build();
}
}
}

