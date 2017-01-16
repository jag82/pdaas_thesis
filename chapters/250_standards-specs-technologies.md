## Standards, Specifications and related Technologies

The overall attempt is to involve as much standards as possible, because it increases the chances
of interoperability and thereby it lowers the effort, that might be needed, in order to integrate
with third parties or other APIs.
Hereinafter, some of these possible technologies will be touched on just briefly, why they might 
be a reasonable choice and what purposes they might going to service.

__[HTTP]{#link_http}__ [@web_spec_http1], well known as the stateless *"transport layer"* for the 
*World Wide Web*, is most likely going to fulfill the same purpose in the context of this work,
because it implements a server-client pattern in it very core.
Whether internal components (local or as part of a distributed system) talk to each other or data 
consumers interact with the system, this protocol transfers the data hat need to be exchanged.
Features introduced with Version 2 [@web_spec_http2] of the protocol are yet to be known of their 
relevance of use cases within this project.
*Websockes* [@web_spec_websockets] might also be a possibility to communicate between components or 
even with external parties, which has the advantage of high efficient ongoing bidirectional 
connections using for real-time data exchange or remotely pending process responses, while at the 
same time avoiding HTTP's long-polling abilities.

__JSON__ [^abbr_json] is an alternative data serialization format to XML, heavily used in web 
contexts to transfer data via *HTTP*, whose syntax is inspired by the JavaScript object-literal 
notation.

The open standard __OAuth__ defines a process flow for authorizing third parties to access
externally hosted resources, such as the user's profile image from *facebook*. The authorisation
validation is done with the help of a previously generated token. However generating and supplying
such a token can be initiated in a variety of ways depending on the situation, e.g. with the user
entering her credentials (`grant_type=authorization_code`). This design mistakenly 
[@web_2012_problem-with-oauth-for-authentication] lead to *OAuth* integrations with the intention to 
provide an authentication service whether as an alternative or as an addition to existing in-house 
solution. Therewith the application authors pass the responsibility on to the OAuth-supporting data 
providers. While *version 1.0a* [@web_spec_oauth-1a], seen as a protocol, provides integrity for 
transferred data by using signatures and confidentiality by encrypting data ahead of transfer. 
Whereas *version 2.0* [@web_spec_oauth-2], labeled as a framework, just requires *TLS*. It also 
includes certain process flows for specific platforms, such as *"web applications, desktop 
applications, mobile phones, and living room devices"* [@web_2016_oauth-2].

With __OpenID__ on the other side, the authenticity of a requesting user gets verified, which
is by design. An in-depth description of the whole process can be found in the protocol's 
same-titled open standard. With decentralisation kept in mind, the protocols's nature encourages 
to design a distributed application architecture, similar to the idea behind *microservices*, but 
without owning all services involved, *decentralized authentication as a service* so to speak. An 
application owner doesn't have to write or implement it's own user management system, instead
it is sufficient to just integrate these parts from the standard need to support signing in with 
*OpenID*. Equally the user is not required to register a new account whenever it is necessary, 
instead she can use her *OpenID*, already created by another identity provider, to authenticate 
with the application. The extension *OpenID Attribute Exchange* allows to import additional profile 
data.
*OpenID Connect* [@web_spec_openid-connect-1] is the third iteration of the OpenID technology
*Connect* is to OpenID what *facebook connect* is to *facebook*, except for the additional
authentication layer, which is build upon *OAuth2.0* and therefore enables, aside from authorisation 
mechanisms, third parties to authenticate an OpenID-user and makes certain data available about 
that account via REST interface.

If it's necessary for certain components, as part of a distributed software, to make them stateless, 
apart from changing the architecture so that the state at that point is not needed anymore, the only 
other option would be to carry the state along (TODO: or "passing the state around"). This is a 
common use case for a __[JSON Web Token]{#link_jwt}__ *(JWT)* [@web_spec_json-web-token]. A *JWT*, 
as it's name implies, is 
syntactically speaking formatted as *JSON*, but URI-safe into *Base64* encoded, before it gets transferred.
The token itself holds the state. Here is where the use of *HTTP* comes in handy, because the 
token can be stored within the HTTP header and therefore can be passed through all communication 
points, where then certain data could be readout and therewith get verified. Such a token typically 
consists of three parts: information about itself, a payload, which can be arbitrary data such as 
user or state information, and a signature; all separated with a period. Additional standards define 
encryption *(JWE [^abbr_jwe])* to ensure confidentiality and signatures *(JWS [^abbr_jws])* to 
preserve integrity of it's contents.
Using a *JWT* for authentication purposes is described as *stateless authentication*, because 
the verifying entity doesn't need to be aware of session IDs nor any information about a state. 
So instead of the backend interface being constrained to check a state (`isLoggedIn(sessionId)` or 
`isAuthorized(sessionId)`) on every incoming request in order to verify permissions, it just needs 

When transferring data over a potential non-private channel several properties might be desired, 
which eventually provide an overall trust to that data. One important aspect might me, that no one 
else expect sender and receiver are able to know and see what the actual data is. To achieve this, 
__Symmetrical Cryptography__ is used for. It states that the sender encrypts the data with the help 
of a key and the receiver decrypts that data also with that key. This is, sender and receiver, both 
need to know that one key, but everyone else should not . To agree on a key without compromising the 
key during that process, both entities either change the medium (e.g meet physically and exchange) 
or have to use a procedure, in which at any point in time the entire key is not exposed to others 
then sender and receiver. This procedure is called __Diffie-Hellman-Key-Exchange__ 
[@paper_1976_d-h-key-exchange] and is based on rules for modulo operations when prime numbers are 
involved. It is designed with the goal to agree on a *secret* while at the same time using a 
non-private channel. The data exchanged during the process alone can't be used to deduce the secret.
Such behaviour is similar to the concepts of __[Asymmetrical Cryptography]{#link_asym-crypto}__ 
*(or public-key cryptography)* [@book_2014_chapter-9-1-public-key-crypto], which is underpinned by a 
*key-pair*; one part is *public* and the other part is *private*. It depends on which of the both 
parts is used to *encrypt* the data, then the other part is used for *decryption*. Combining this 
approach with the idea of digital signatures (encrypted fingerprints of the data), then provides 
integrity and authentication.

Wrapping *HTTP* in the *Transport Layer Security* [@web_spec_tls] (*TLS*) results in __HTTPS__. TLS 
provides encryption during the data transport, which reduces the vulnerability to 
*man-in-the-middle* attacks and thus ensures not only confidentiality but data integrity as well. 
*Asymmetric cryptography* is the foundation for the connection establishment, hence *TLS* also 
allows to verify integrity of the entity on the the connection's counterside, and, depending on the 
integration, it could even be used for authentication purposes. But relying on those cryptographical
concepts requires additional infrastructure. Such an infrastructure is known as *Public Key 
Infrastructure* (or *PKI*) [@book_2014_chapter-14-5-pki]. It manages and provides public keys in a 
directory, including related information to the owners of these certificates. A Certificate 
Authority (or *CA*), as part of that infrastructure, issues, maintains and revokes digital 
certificates. The infrastructure that is needed to provide secure HTTP connections for the internet 
is one of those *PKI*s - a public one and probably the largest. It is based on the widely used IETF 
[^abbr_ietf] standard *X.509* [@web_spec_x509].

__REST(ful)__ [^abbr_rest] is a common set of principles to design web resources communication, 
primarily server-client relations, in a more generic and thereby interoperable way. Aside from 
hierarchically structured URIs, which reflect semantic meanings, it involves a group of rudimentary 
vocabulary [^http_methods] to provide basic Create-Read-Update-Delete operations across distributed
systems. The entire request need to contain everything that is required to get proceeded, e.g. state 
data and possibly authentication. These operation normally wont get applied directly to the 
responsible component. Instead the whole system (or certain services) exposes a restful API, with
which a third party can then interact. 

The *QL* in __GraphQL__ [@web_spec_graphql] stands for *query language*. It's goal is to abstract
multiple data sources in order to unify them under one API and make all containing data queryable,
including all relating data points. The returned data, emitted in JSON syntax, can exhibit 
graph-like structures, meaning multiple data points, that might be somehow related to each other, or 
in other words: indirectly "linked" through each other. These, naturally deep-leveled structures,
can be described by the syntax of the query language.

The term __Semantic Web__ bundles a conglomerate of standards addressing syntax, schemas, assess
control and integration around the idea of *web of data* to *"allow data being shared and reused 
across"* [web_2016_w3c_semantic-web-activity] or within several scopes and contexts.
Alongside several others, the following three standards have a certain relevance to that  concept.
RDF [^abbr_rdf] basically defines the syntax. OWL [^abbr_owl] provides the guidelines on how the 
semantics and schemas should be defined and with SPARQL [@web_w3c-tr_sparql], the query language for 
the RDF format, the data can be retrieved.
A picture emerges in which the web is used as a database, queried by URIs with a query language.
An example would be a person's email address, which is available under a specific domain (preferable 
owned by that person) - or to be more precise, an URI *(WebID) [@web_w3c-draft_webid]* - and 
provided in a certain syntax *(RDF)* and tagged with the semantic *(OWL)* of a email address; all 
embedded in a valid html page. This information can be queried *(SPARQL)*, which requires at least 
the URI, working as a unique identifier. 
While defining the standards, an importancy was to define a syntax which is also valid markup, in 
order to maintain a single source of trough and save redundant work.
Related to this topic is the work on a specification called __Solid__ [^abbr_solid]. Based on the 
*Linked Data* principles, that are facilitated through the standards just mentioned and the 
*WebAccessControl* [@web_2016_wiki_webaccesscontrol] system, the project focuses on decentralization 
and personal data. A reference implementation called *databox* [@web_2016_demo_databox] combines all 
these technologies and is build on top. 

The concept of application (or software) __container__ is about encapsulating runtime environments 
by introducing an additional layer of abstraction. A container bundles just the software 
dependencies (e.g. binaries) that are absolutely necessary so that the enclosed program is able to 
run properly. The actual container separation is done, aside from others, with the help of two 
features provided by the Linux kernel. *Cgroups* [^abbr_cgroups], which define or restrict how much 
of the existing resources a group of processes (e.g. CPU, memory or network) can use. Whereas 
*namespaces* [@web_2016_kernel-namespace] define or restrict what parts of the system can be 
accessed or seen by a process (e.g. filesystem, user, other processes).
The idea of encapsulating programs from the operating system-level is not new, Technologies, such as 
*libvirt*, *systemd-nspawn*, *jails*, or *hypervisors* (e.g. VMware, KVM, virtualbox) have been used 
for years, but were usually too cumbersome and never reached a great level of convenience, so that 
only people with a certain expertise were able to handle systems build upon virtualization, but 
people with other backgrounds couldn't and weren't that much interested. Until *Docker* and *rkt* 
emerged. After some years of separated work, both authors, and others, recently joined forces in the 
*Open Container Initiative* [@web_2016_open-container-initiative], which aims to harmonize the 
diverged landscape and start building common ground to ensure a higher interoperability, and that in 
turn is requisite for orchestration. It also marks the initial draft of the specifications for 
runtime [@web_oci-spec_runtime] and image [@web_oci-spec_image] definition, on which the work is 
still ongoing.
This concept of *containerization* also inherits the a ability known from *emulation*, because it 
allows a certain set of software to run on a system that otherwise is not supported, e.g. mobile 
devices. It only requires the runtime to be working.

In the past years different countries around the world started to introduce *information technology* 
to the day-to-day processes, interactions and communications between public services and their 
citizens, for example changing residence information or filing tax report, which is summarized 
under the term *E-government* [^abbr_egov]. One of those developments is the so called __Electronic 
ID Card__{#link_eid-card}, hereinafter called *eID card*. Equipped with storage, logic and 
interfaces for wireless communication, those *eID cards* can be used to store certain information 
and digital keys or to authenticate the owner electronically to a third party without being 
physically present. Such an *eID card* was also introduced in Germany in 2010. The so called *nPA* 
[^abbr_npa] was an important step towards an operational *e-government*.
Aside from minor flaws [@web_2013_npa-sicherheitsdefizit] and disadvantages [@web_2014_test-qes-support-in-npa] 
an *eID card* can come with, the question here is, how can such technology be usefully integrated in 
this project and does it even makes sense. As an official document the card has one major advantage 
over self-configured or generated authentication mechanisms like passwords, fingerprints or TANs 
[^abbr_tan]. It is *signed* by design, which means, by creating this document and handing it over 
to the related citizen, the third party (or *"authority"*) - in this case the government - has 
verified the authenticity of that individual.

When communication via email it is already common to encrypted the transport channel, but using
*asymmetric cryptography* for encrypting emails end-to-end is rather unusual. The equivalent to a 
*PKI* would be basically a *public key server* that follows a concept called *web of trust*. In 
which all entities (user; senders and recipients) are signing each other's public keys. The more 
users have signed a public key, the higher the trust that this key actually belongs to the user that 
it says it does. That public key is then simply uploaded by the owner to public servers where
another user, who wants to write that key owner an email can obtain keys.
Related to that topic, another technology emerging as part of the *e-government* development, 
is the german __De-Mail__ [@web_2017_about-de-mail]. It's an eMail-Service that is meant to provide 
infrastructure and mechanisms to exchange legally binding electronic documents. One would expect 
a *public key cryptography*-based implementation all the way from sender through to the recipient 
[@statement_2013_de-mail], maybe even with taking advantage of the *nPA's* capability to create 
*QES*, which refers to the ability of using the *nPA* to sign arbitrary data. Instead, the creators 
of the corresponding law decided that it's enough to prove the author's identity if the provider 
signs the document on the email server and that this implementation results in a legally binding 
document by definition of that law. 



[^abbr_json]: The JavaScript Object Notation (JSON) Data Interchange Format; ECMA Standard  
    [@web_spec_json] and Internet Engineering Task Force RFC 7159 [@web_rfc_json]

[^abbr_jwe]: JSON Web Encryption, Internet Engineering Task Force RFC 7516 
    [@web_spec_json-web-encryption]

[^abbr_jws]: JSON Web Signature, Internet Engineering Task Force RFC 7515 
    [@web_spec_json-web-signature]
    
[^abbr_ietf]: Internet Engineering Task Force; non-profit organisation that develops and releases 
    standards mainly related to the Internet protocol suite

[^abbr_rest]: *Representational State Transfer*, introduces by Roy Fielding in his doctoral 
    dissertation [@web_spec_rest] 

[^http_methods]: knows as HTTP Methods or Verbs [@web_spec_http-methods] (e.g. GET, OPTIONS, PUT, 
    DELETE)
    
[^abbr_rdf]: Resource Description Framework [@web_w3c-tr_rdf]

[^abbr_owl]: Web Ontology Language [@web_w3c-tr_owl]

[^abbr_solid]: social linked data [@web_spec_solid]

[^abbr_cgroups]: control groups [@web_2015_cgroup-doc]

[^abbr_egov]: Electronic government

[^abbr_npa]: in german so called *elektronische Personalausweis (nPA)*

[^abbr_tan]: Transaction authentication number

[^abbr_qes]: Qualified Electronic Signatures [@paper_2013-keymanangement-fuer-qes-mit-npa]