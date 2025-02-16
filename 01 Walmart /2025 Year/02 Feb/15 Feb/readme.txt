Learnings Tab


15th feb 


Learnt when I saw a new error 

I wanted to add a field in  a DTO inside  mcse-commons and generate a new version using mvn version change ( which changes version ) 
and then build it , to have that and use that specific version in my ./m2 file to use it in my code to debug . 

While building it i was getting a error 
@XMLTransient annotation not found . 



I was beating my head over it . 

Learnings : 

Then i realised that it is a javax feature and only supported by jdk1.8 and not 17 . 

So I changed the project structure to jdk1.8 and reran the build , 
also i did not run the build from terminal maven command . I used the debug or edit configuration scenario and added the plugin of maven to build it and print it . 






15th Feb 


I was working on the solution generation repo , i made some code changes and generated a custom version , and was using that in mcse lite 
In mcse lite we have the .class file (decompiled bytcode )

While i was debugging it because very difficult to debug , it was moving very slow and variables , like SolutionGenerationCcmContext took very long to compile 

Chatgpt told me : Debugging a decompiled JAR can be slow for multiple reasons, especially if it’s a large file or if the debugger struggles with mapping bytecode back to readable source code 

So i tried attaching the jar , in intellIj you get an option to attach a jar file , and i was attaching this file 
solution-generation-frequency-based-0.0.22-SNAPSHOT-SPLIT-INV-FIX.jar 

But it did not accept it 

Then i read that we have to attach a ...-sources.jar file , 

solution-generation-frequency-based-0.0.22-SNAPSHOT-SPLIT-INV-FIX-sources.jar 

.

But during me building solution repo , this sources file was not getting generated , 

Talked to chatgpt , it told me a pluging needs to be generated maven-source 

<apache.maven-source-plugin.version>3.3.1</apache.maven-source-plugin.version> ---> version i saw on maven site 

<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-source-plugin</artifactId>
	<version>${apache.maven-source-plugin.version}</version>  <!-- Ensure latest version -->
	<executions>
		<execution>
			<id>attach-sources</id>
			<phase>package</phase>  <!-- Ensures source JAR is built during packaging -->
			<goals>
				<goal>jar</goal>
			</goals>
		</execution>
	</executions>
</plugin>



In the pom of soln-gen 

Then i built it and this file was generated 
solution-generation-frequency-based-0.0.22-SNAPSHOT-SPLIT-INV-FIX-sources.jar  

This i attached in mcse-lite and i got java code 
