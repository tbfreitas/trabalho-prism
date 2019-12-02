// Trabalho de dependabilidade de software
// Objetivo: trabalho visa modelar um sistema de integração contínua.
dtmc

const double git_chance_turn_off_server = 1/27;
const double continuous_chance_turn_off_server = 1/19;
const double build_chance_turn_off_server = 1/33;

const double sintatic_error_on_code = 1/3;

module hasInternet
		
	// A maquina possui conexao com a internet
	// 1 - erro de conexao
	// 2 - conexao com sucesso
	hasInternetAccess : [0..2] init 0; 
	
	[] hasInternetAccess = 0 -> 0.01:(hasInternetAccess'=1) + 0.99:(hasInternetAccess'=2);		
	[] hasInternetAccess = 1 -> 1:(hasInternetAccess'=1) ;		
	[connected] hasInternetAccess = 2 -> 1:(hasInternetAccess'=2);

	
endmodule

module developerComputerHasInternet = hasInternet [hasInternetAccess = computerHasInternetAccess] endmodule

module gitServerOnAir
	
	// 1 - com problema
	// 2 - servidor git no ar
	gitServerStatus: [0..2] init 0;
						 
	[connected] gitServerStatus = 0 & (hasInternetAccess = 2) -> git_chance_turn_off_server:(gitServerStatus'=1) + (1 - git_chance_turn_off_server):(gitServerStatus'=2); 
	[] gitServerStatus = 0 & (hasInternetAccess = 1) -> 1:(gitServerStatus'=1);
	[] gitServerStatus = 1 -> 1:(gitServerStatus'=1);
	[onAir] gitServerStatus = 2 -> 1:(gitServerStatus'=2);
	
endmodule
	
module continousServerOnAir = gitServerOnAir [gitServerStatus = continuousServerStatus, git_chance_turn_off_server = continuous_chance_turn_off_server,gitOnAir = continousOnAir] endmodule

module updateCodeAndCommit

	// 1 - problema no update e commit
	// 2 - updateCode and commit com sucesso 
	updateCodeAndCommit: [0..2] init 0;
		
	[onAir] updateCodeAndCommit = 0 & (computerHasInternetAccess = 2) & (gitServerStatus = 2 ) -> 1:(updateCodeAndCommit'=2);
	[] updateCodeAndCommit = 0 & (computerHasInternetAccess = 1) | (gitServerStatus = 2 ) ->  1:(updateCodeAndCommit'=2);
	[] updateCodeAndCommit = 1 -> 1:(updateCodeAndCommit'=1);
	[commited] updateCodeAndCommit = 2 -> 1:(updateCodeAndCommit'=2);

endmodule

module buildOnAir = gitServerOnAir [gitServerStatus = buidServerStatus, git_chance_turn_off_server = build_chance_turn_off_server ,gitOnAir = buildOnAir] endmodule
module buildDeploy
	
	// 0 - buidServerStatus está no ar, entao verifica erro sintatico.
	// 1 - verifica erro sintatico no codigo
	// 2 - verifica erro de falta de capacidade.
	// 3 - buildado com sucesso.
	// 4 - deployado com sucesso. Estado final
	// 5 - erro no processo

	buildStatusServer: [0..6] init 0;
	
	[commited] buildStatusServer = 0 & (buidServerStatus = 2) -> sintatic_error_on_code:(buildStatusServer'= 5) + (1 - sintatic_error_on_code):(buildStatusServer'=1);
	[] buildStatusServer = 0 & (buidServerStatus = 1) -> 1:(buildStatusServer'=5);	
	[] buildStatusServer = 1 -> 0.935:(buildStatusServer'=2) + 0.065:(buildStatusServer'=5);
	[] buildStatusServer = 2 -> 0.95:(buildStatusServer'=3) + 0.05:(buildStatusServer'=5);
	[] buildStatusServer = 3-> 1:(buildStatusServer'=4);
	[terminate] buildStatusServer = 4 -> 1:(buildStatusServer'=4);
	[] buildStatusServer = 5 -> 1:(buildStatusServer'=5); 

endmodule 

label "terminate" = buildStatusServer = 4; 







		
		

	
	