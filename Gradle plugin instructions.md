<h3 id="gradle-build-plugin">Gradle Build Plugin</h3>
<p>If your app does not build with gradle, skip to <a href="#command-line-build-tool">Integrating with the
Command Line Tool</a>. </p>
<p>The App SDK plugin is distributed as part of the SDK as
<strong>GradlePlugin/com.microsoft.intune.mam.build.jar</strong>. For Gradle to be
able to find the plugin, it must be added to the buildscript
classpath. The plugin depends on
<a href="http://jboss-javassist.github.io/javassist/">Javassist</a>, which must
also be added. To add these to the classpath, add the following to
your root <code>build.gradle</code></p>
<pre><code class="lang-groovy">buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath &quot;org.javassist:javassist:3.22.0-GA&quot;
        classpath files(&quot;$PATH_TO_MAM_SDK/GradlePlugin/com.microsoft.intune.mam.build.jar&quot;)
    }
}
</code></pre><p>Then, in the <code>build.gradle</code> file for your APK project, simply apply the plugin as</p>
<pre><code class="lang-groovy">apply plugin: &#39;com.microsoft.intune.mam&#39;
</code></pre><p>By default, the plugin will operate <strong>only</strong> on <code>project</code> dependencies.
Test compilation not affected. Configuration may be provided to list</p>
<ul>
<li>Projects to exclude</li>
<li><a href="#usage-of-includeexternallibraries">External dependencies to include</a> </li>
<li>Specific classes to exclude from processing</li>
<li>Variants to exclude from processing. These can refer to either a
complete variant name or a single flavor. For example<ul>
<li>if your app has build types <code>debug</code> and <code>release</code> with flavors
{<code>savory</code>, <code>sweet</code>} and {<code>vanilla</code>, <code>chocolate</code>} you could specify</li>
<li><code>savory</code> to exclude all variants with the savory flavor or
<code>savoryVanillaRelease</code> to exclude only that exact variant.</li>
</ul>
</li>
</ul>
<h4 id="example-partial-buildgradle">Example partial build.gradle</h4>
<pre><code class="lang-groovy">
apply plugin: &#39;com.microsoft.intune.mam&#39;

dependencies {
    implementation project(&#39;:product:FooLib&#39;)
    implementation project(&#39;:product:foo-project&#39;)
    implementation fileTree(dir: &quot;libs&quot;, include: [&quot;bar.jar&quot;])
    implementation fileTree(dir: &quot;libs&quot;, include: [&quot;zap.jar&quot;])
    implementation &quot;com.contoso.foo:zap-artifact:1.0.0&quot;
    implementation &quot;com.microsoft.bar:baz:1.0.0&quot;

    // Include the MAM SDK
    implementation files(&quot;$PATH_TO_MAM_SDK/Microsoft.Intune.MAM.SDK.aar&quot;)
}
intunemam {
    excludeProjects = [&#39;:product:FooLib&#39;]
    includeExternalLibraries = [&#39;bar.jar&#39;, &quot;com.contoso.foo:zap-artifact&quot;, &quot;com.microsoft.*&quot;]
    excludeClasses = [&#39;com.contoso.SplashActivity&#39;]
    excludeVariants=[&#39;savory&#39;]
}
</code></pre><p>This would have the following effects:</p>
<ul>
<li><code>:product:FooLib</code> is not rewritten because it is included in <code>excludeProjects</code></li>
<li><code>:product:foo-project</code> is rewritten, except for <code>com.contoso.SplashActivity</code> which is skipped because it&#39;s in <code>excludeClasses</code></li>
<li><code>bar.jar</code> is rewritten because it is included in <code>includeExternalLibraries</code></li>
<li><code>zap.jar</code> is <strong>not</strong> rewritten because it&#39;s not a project and it&#39;s not included in <code>includeExternalLibraries</code></li>
<li><code>com.contoso.foo:zap-artifact:1.0.0</code> is rewritten because it&#39;s included in <code>includeExternalLibraries</code></li>
<li><code>com.microsoft.bar:baz:1.0.0</code> is rewritten because it&#39;s included in <code>includeExternalLibraries</code> via a wildcard (<code>com.microsoft.*</code>).</li>
</ul>
<h4 id="usage-of-includeexternallibraries">Usage of includeExternalLibraries</h4>
<p>Since the plugin only operates on project dependencies (usually
provided by the <code>project()</code> function) by default, any dependencies
specified by <code>fileTree(...)</code> or obtained from maven or other package
sources (e.g. &quot;<code>com.contoso.bar:baz:1.2.0</code>&quot;) must be
provided to the <code>includeExternalLibraries</code> property if MAM processing
of them is needed based on the criteria explained below. Wildcards (&quot;*&quot;)
are supported.</p>
<p>When specifying external dependencies with artifact notation, it is
recommended to omit the version component in the
<code>includeExternalLibraries</code> value. If you do include the version, it
must be an exact version. Dynamic version specifications (e.g. <code>1.+</code>) are not supported.</p>
<p>The general rule you should use to determine if you need to include
libraries in <code>includeExternalLibraries</code> is based on two questions:</p>
<ol>
<li>Does the library have classes in it for which there are MAM equivalents? Examples: <code>Activity</code>, <code>Fragment</code>, <code>ContentProvider</code>, <code>Service</code> etc.</li>
<li>If yes, does your app make use of those classes?</li>
</ol>
<p>If you answer &#39;yes&#39; to both of those questions, then you must include that library in <code>includeExternalLibraries</code>. </p>
<table>
<thead>
<tr>
<th>Scenario</th>
<th>Should Include?</th>
</tr>
</thead>
<tbody>
<tr>
<td>You include a PDF viewer library in your app and you use the viewer <code>Activity</code> in your application when users try to view PDFs</td>
<td>Yes</td>
</tr>
<tr>
<td>You include an HTTP library in your app for enhanced web performance</td>
<td>No</td>
</tr>
<tr>
<td>You include a library like React Native that contains classes derived from <code>Activity</code>, <code>Application</code> and <code>Fragment</code> and you use or further derive those classes in your app</td>
<td>Yes</td>
</tr>
<tr>
<td>You include a library like React Native that contains classes derived from <code>Activity</code>, <code>Application</code> and <code>Fragment</code> but you only use static helpers or utility classes</td>
<td>No</td>
</tr>
<tr>
<td>You include a library that contains view classes derived from <code>TextView</code> and you use or further derive those classes in your app</td>
<td>Yes</td>
</tr>
</tbody>
</table>
<h4 id="dependencies">Dependencies</h4>
<p>The gradle plugin has a dependency on
<a href="http://jboss-javassist.github.io/javassist/">Javassist</a>, which must
be available to Gradle&#39;s dependency resolution (as described
above). Javassist is used solely at build time when running the
plugin. No Javassist code will be added to your app.</p>
<p>[!NOTE] You must be using version 3.0 or newer of the Android Gradle plugin and
Gradle 4.1 or newer.</p>
<h3 id="command-line-build-tool">Command Line Build Tool</h3>
<p>If your build uses Gradle, skip to the <a href="#class-and-method-replacements">next section</a>.</p>
<p>The command-line build tool is available in the <code>BuildTool</code> folder of
the SDK drop. It performs the same function as the Gradle plugin
detailed above, but can be integrated into custom or non-Gradle build
systems. As it is more generic, it is more complex to invoke, so the
Gradle plugin should be used when it is possible to do so.</p>
<h4 id="using-the-command-line-tool">Using the Command Line Tool</h4>
<p>The command line tool can be invoked by using the provided helper scripts
located in the <code>BuildTool\bin</code> directory.</p>
<p>The tool expects the following parameters.</p>
<table>
<thead>
<tr>
<th>Parameter</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>--input</code></td>
<td>A semi-colon delimited list of jar files and directories of class files to modify. This should include all jars/directories that you intend to rewrite.</td>
</tr>
<tr>
<td><code>--output</code></td>
<td>A semi-colon delimited list of jar files and directories to store the modified classes to. There should be one output entry per input entry, and they should be listed in order.</td>
</tr>
<tr>
<td><code>--classpath</code></td>
<td>The build classpath. This may contains both jars and class directories.</td>
</tr>
<tr>
<td><code>--excludeClasses</code></td>
<td>A semi-colon delimited list containing the names of the classes that should be excluded from rewriting.</td>
</tr>
</tbody>
</table>
<p>All parameters are required except for <code>--excludeClasses</code> which is optional.</p>
<h4 id="example-command-line-tool-invocation">Example Command Line Tool invocation</h4>
<pre><code class="lang-batch">&gt; BuildTool\bin\BuildTool.bat --input build\product-foo-project;libs\bar.jar --output mam-build\product-foo-project;mam-build\libs\bar.jar --classpath build\zap.jar;libs\Microsoft.Intune.MAM.SDK\classes.jar;%ANDROID_SDK_ROOT%\platforms\android-27\android.jar --excludeClasses com.contoso.SplashActivity
</code></pre><p>This would have the following effects:</p>
<ul>
<li>the <code>product-foo-project</code> directory is rewritten to <code>mam-build\product-foo-project</code></li>
<li><code>bar.jar</code> is rewritten to <code>mam-build\libs\bar.jar</code></li>
<li><code>zap.jar</code> is <strong>not</strong> rewritten because it is only listed in <code>--classpath</code></li>
<li>The <code>com.contoso.SplashActivity</code> class is <strong>not</strong> rewritten even if it is in <code>--input</code></li>
</ul>
<div class="NOTE"><h5>Note</h5><p>The build tool does not currently support aar files. If your build
system does not already extract <code>classes.jar</code> when dealing with aar files, you
will need to do so before invoking the build tool.</p>
</div>